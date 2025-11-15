# 🏗️ Arquitectura Integral de Datos para COVALTO  
**Por:** Alejandra Hincapié Garzón  
**Cargo:** Gerente de Ingeniería de Datos e IA — Liderazgo técnico y sistémico  

---

## 🌐 Modelo en Capas y Agnóstico de Nube

Esta arquitectura está diseñada **en capas**, lo que permite:

- Separar responsabilidades: ingestión, limpieza, semántica y productos de datos.  
- Facilitar mantenibilidad, escalabilidad y adopción de nuevas tecnologías sin romper procesos existentes.  
- Aislar cambios: se pueden mejorar capas individuales sin afectar al resto.  

Además, es **agnóstica de nube**, lo que significa que **puede implementarse en AWS, GCP, Azure o entornos híbridos**, usando servicios equivalentes según disponibilidad y costos.

---

## 🎯 Propósito de esta Propuesta

Diseñar una **arquitectura moderna, escalable y segura** que permita a COVALTO habilitar:

- Autonomía para que los equipos de negocio puedan analizar sus datos  
- Creación de características robustas para modelos de riesgo  
- Ingesta y monitoreo en tiempo real de eventos de fraude  
- Gobernanza, calidad y trazabilidad exigidas en un banco regulado  

Esta solución integra simultáneamente **necesidades de negocio**, **restricciones operativas**, y **madurez técnica**, ofreciendo una visión completa del ecosistema de datos.

---

# 🧩 1. Casos de Uso y Valor Estratégico

### **1️⃣ Visualización del Comportamiento del Cliente**
**Dolores actuales:**  
- Solicitudes ad-hoc que retrasan la toma de decisiones.  
- Confusión por ambigüedad semántica entre fuentes.  
- Datos dispersos en múltiples sistemas.  

**Soluciones:**  
- **Self-service BI** con datos normalizados.  
- **Modelo semántico unificado** y diccionario de datos.  
- **Ingesta consolidada** y catálogo de datos.  

**Impacto en negocio:**  
> Permite decisiones ágiles y basadas en datos, aumentando retención de clientes y detectando oportunidades de cross-selling.

---

### **2️⃣ Evaluación de Riesgo para Hipotecas**
**Dolores actuales:**  
- Modelos dependientes de datos crudos, difíciles de reproducir.  
- Procesos no estandarizados ni trazables.  

**Soluciones:**  
- **Feature Store** con versionamiento de características.  
- Pipelines reproducibles en **batch y streaming**.  
- Linaje completo y auditoría automática.  

**Impacto en negocio:**  
> Reducción de errores de modelado y tiempo de desarrollo de modelos, mejorando precisión en decisiones de riesgo y cumplimiento regulatorio.

---

### **3️⃣ Monitoreo de Fraude en Tiempo Real**
**Dolores actuales:**  
- Latencia alta y accesos inconsistentes a datos críticos.  
- Procesamiento poco confiable.  
- Fragmentación de acceso a características utilizadas por modelos.  

**Soluciones:**  
- **Arquitectura basada en eventos** y pipelines streaming.  
- **Feature Store** accesible en batch y tiempo real.  
- Alertas automáticas por inconsistencias o retrasos.  

**Impacto en negocio:**  
> Protección inmediata de activos financieros y fortalecimiento de la confianza del cliente.

# 🏛️ 2. Principios de Diseño

- **Datos como Producto:** responsable, documentación, SLA y contratos claros.  
- **Semántica compartida:** diccionario y modelo común.  
- **Arquitectura en capas:** responsabilidades separadas y mantenibles.  
- **Procesamiento batch y en tiempo real:** estrategia según necesidad.  
- **Trazabilidad y gobernanza desde el diseño:** datos auditables y rastreables.  
- **Escalabilidad horizontal:** manejo eficiente de APIs y paralelismo.  
- **Calidad de datos:** validaciones automáticas, alertas y métricas de desempeño.  

---

# 🏗️ 3. Arquitectura Propuesta (Visión 360°)

## 🥇 **Capa 0 — Ingesta Unificada (Batch + Streaming + CDC)**

| Fuente                                           | Tipo                           | Acceso                                            | Estrategia                                                                     |
| ------------------------------------------------ | ------------------------------ | ------------------------------------------------- | ------------------------------------------------------------------------------ |
| Declaraciones de Impuestos Anuales               | API XML                        | Token + autorización digital                      | Ingestión batch, control de límites, procesamiento ETL a Data Lake raw         |
| Historial de Transacciones de Tarjeta de Crédito | API JSON / PostgreSQL interno  | API: token, bulk & streaming; DB: baja integridad | Pipelines streaming desde API; ETL para DBs internas con limpieza y validación |
| Extractos Bancarios                              | API XML / S3 (PDFs e imágenes) | API: token + firma; S3: no estructurado           | ETL batch/streaming para XML; OCR y ML para PDFs e imágenes                    |

**Notas adicionales:**

- Declaraciones de Impuestos: colas de trabajo para paralelizar llamadas sin exceder límites.  
- Transacciones de Tarjeta: captura de cambios (CDC) para mantener datos actualizados; validación de integridad y consistencia.  
- Extractos Bancarios: XML → parseo estructurado; PDFs/Imágenes → OCR + NLP para extracción; validaciones de completitud y consistencia.  

**Tecnologías sugeridas:** Kafka / PubSub, Airflow / Databricks Jobs, S3 / GCS con Parquet / Delta, OCR (AWS Textract / GCP Document AI), Spark / Beam, Debezium / Fivetran  

---

## 🥈 **Capa 1 — Normalización y Limpieza de Datos**

Objetivo: **eliminar ambigüedad y unificar la semántica de todos los datos**.

Incluye:

- Tipificación de datos  
- Estandarización de fechas, valores monetarios e identificadores  
- Detección de duplicados  
- Validación contra reglas de negocio  
- Auditoría y linaje automático  
- Validaciones de calidad: completitud, consistencia, patrones de datos y alertas  

📌 **Resultado:** Tablas limpias y consistentes, listas para análisis.

---

## 🥉 **Capa 2 — Modelo Semántico Empresarial**

| Entidad      | Descripción                                                   |
| ------------ | ------------------------------------------------------------- |
| Cliente      | Información única del cliente: perfil, segmentación y comportamiento |
| Cuenta       | Detalles de cuentas financieras y relaciones con el cliente   |
| Transacción  | Registros de movimientos y pagos                               |
| Comportamiento de crédito | Métricas derivadas de riesgo y cumplimiento de pagos |
| Métricas financieras derivadas | Indicadores de negocio y agregaciones financieras |

**Beneficios:**  
- Elimina confusiones semánticas  
- Facilita el análisis de negocio  
- Garantiza consistencia en riesgo y fraude  
- Mantiene datos actualizados mediante CDC  

---

## 🏅 **Capa 3 — Productos de Datos según Caso de Uso**

### **A. Visualización y BI del Cliente**
- Exposición de datos normalizados y validados  
- Diccionario de datos actualizado  
- Acceso controlado según permisos  
- Alertas automáticas si los datos pierden consistencia  

🎯 Analistas pueden crear reportes sin depender de ingeniería.

---

### **B. Feature Store para Modelos de Riesgo**
- Almacenamiento de características con versionamiento  
- Tiempo de validez de cada característica  
- Procesamiento batch y en streaming sincronizado  
- Validaciones de calidad y consistencia de los datos  

🎯 Garantiza reproducibilidad, equidad y precisión en los modelos de riesgo.

---

### **C. Monitoreo de Fraude en Tiempo Real**
- Transformaciones y enriquecimiento de datos en tiempo real  
- Cálculo de puntuaciones de riesgo en línea  
- Almacenamiento temporal para colas de eventos  
- Detección de patrones mediante reglas y modelos predictivos  
- Alertas automáticas ante inconsistencias o retrasos  

🎯 Velocidad y precisión para proteger la operación bancaria.

---

# 🛡️ 4. Gobernanza, Calidad y Confianza

- Catálogo y diccionario de datos  
- Gestión de acceso basada en roles y cumplimiento regulatorio  
- Contratos de datos entre equipos  
- Monitoreo de procesos con métricas y alertas  
- Validaciones automáticas de calidad  
- Linaje completo desde origen hasta consumo  
- **Cultura de datos:** entrenar equipos, empoderar decisiones correctas y promover propiedad compartida  

**KPIs sugeridos:**  
- Latencia de pipelines: batch <1h, streaming <5s  
- Calidad de datos: % de registros completos, % de duplicados detectados  
- Disponibilidad de features: >99%  
- Incidentes críticos: <1 por mes  

---

# 🧠 5. Diagrama de Arquitectura

```mermaid
flowchart LR

%% ==================== Fuentes ====================
subgraph Sources["🔹 Fuentes de Datos"]
    A[Declaraciones Anuales XML]
    B[Transacciones de Tarjetas JSON]
    C[Bases de Datos Internas]
    D[Estados de Cuenta XML]
    E[PDFs e Imágenes en S3]
end

%% ==================== Capa 0 - Ingesta ====================
subgraph Ingestion["🟦 Capa 0 - Ingesta y CDC"]
    A -->|Batch| I1[Regulación + Colas de Trabajo]
    B -->|Streaming| I2[Ingesta Streaming + CDC]
    C -->|Batch| I3[Extracción Batch + Calidad]
    D -->|Batch| I4[Parseo XML]
    E -->|Batch/Streaming| I5[OCR + NLP]
end

%% ==================== Capa 1 - Limpieza ====================
subgraph Processing["🟩 Capa 1 - Limpieza y Normalización"]
    I1 --> C1[Normalización + Validación]
    I2 --> C1
    I3 --> C1
    I4 --> C1
    I5 --> C1
end

%% ==================== Capa 2 - Modelo Semántico ====================
subgraph Semantic["🟨 Capa 2 - Modelo"]
    C1 --> S1[Cliente]
    C1 --> S2[Cuenta]
    C1 --> S3[Transacción]
    C1 --> S4[Crédito]
    C1 --> S5[KPI Finanzas]
end

%% ==================== Capa 3 - Productos de Datos ====================
subgraph Products["🟧 Capa 3 - Productos"]
    S1 --> BI[Visualización / BI]
    S3 --> FR[Fraude Tiempo Real]
    S4 --> FS[Feature Store]
end
