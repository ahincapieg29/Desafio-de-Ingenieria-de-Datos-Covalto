# 🏗️ Arquitectura Integral de Datos para COVALTO  
### **Por: Alejandra Hincapié Garzón**  
**Gerente de Ingeniería de Datos e IA — Liderazgo técnico, estratégico y sistémico**

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

# 🧩 1. Resumen de los Casos de Uso

### **1️⃣ Visualización del Comportamiento del Cliente**
**Dolores actuales:**  
- Solicitudes ad-hoc que generan retrasos → **Solución:** self-service BI con datos normalizados.  
- Diferencias de significado entre fuentes → **Solución:** modelo semántico único.  
- Datos dispersos en múltiples sistemas → **Solución:** ingesta unificada y catálogo de datos.  
- Ausencia de un modelo de datos común → **Solución:** diccionario de datos y modelo empresarial.  

---

### **2️⃣ Evaluación de Riesgo para Hipotecas**
**Dolores actuales:**  
- Modelos dependientes de datos crudos → **Solución:** Feature Store con características versionadas.  
- Procesos no repetibles → **Solución:** pipelines reproducibles batch y streaming.  
- Falta de trazabilidad → **Solución:** linaje completo y auditoría automática.  

---

### **3️⃣ Monitoreo de Fraude en Tiempo Real**
**Dolores actuales:**  
- Latencia alta y accesos inconsistentes → **Solución:** ingestión streaming con baja latencia y acceso unificado.  
- Procesamiento poco confiable → **Solución:** arquitectura basada en eventos con alertas automáticas.  
- Acceso fragmentado a características → **Solución:** Feature Store accesible desde tiempo real y batch.  

---

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

Creación de modelos con significados únicos:

- Cliente  
- Cuenta  
- Transacción  
- Comportamiento de crédito  
- Métricas financieras derivadas  

**Beneficios:**  
- Elimina confusiones semánticas  
- Facilita el análisis por parte de negocio  
- Aporta entendimiento estándar a riesgo y fraude  
- Mantiene los datos actualizados mediante captura de cambios (CDC)

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

🎯 **Beneficio:** datos confiables y rastreables para BI, riesgo y fraude.

---

# 🧠 5. Diagrama de Arquitectura

flowchart LR
    %% Columnas como subgraphs para organización visual

    subgraph S1["Fuentes de Datos"]
        direction TB
        A[Declaraciones Anuales XML]
        B[Transacciones de Tarjetas JSON]
        C[Bases de Datos Internas]
        D[Extractos Bancarios XML]
        E[PDFs e Imágenes en S3]
    end

    subgraph S2["Capa 0 - Ingesta y CDC"]
        direction TB
        I1[Regulación + Colas de Trabajo]
        I2[Ingesta en Streaming + Captura de Cambios]
        I3[Extracción Batch + Reglas de Calidad + Captura de Cambios]
        I4[Parseo XML]
        I5[OCR y Extracción NLP]
    end

    subgraph S3["Capa 1 - Limpieza y Normalización"]
        direction TB
        C1[Normalización + Validación de Calidad]
    end

    subgraph S4["Capa 2 - Modelo Semántico"]
        direction TB
        S1c[Cliente]
        S2c[Cuenta]
        S3c[Transacción]
        S4c[Métricas Financieras]
    end

    subgraph S5["Capa 3 - Productos de Datos"]
        direction LR
        subgraph P1["Visualización y BI"]
            direction TB
            P1a[Visualización y BI]
        end
        subgraph P2["Motor de Fraude"]
            direction TB
            P2a[Motor de Fraude en Tiempo Real]
        end
        subgraph P3["Feature Store"]
            direction TB
            P3a[Feature Store para Riesgo]
        end
    end

    %% Conexiones Fuentes -> Ingesta
    A --> I1
    B --> I2
    C --> I3
    D --> I4
    E --> I5

    %% Ingesta -> Limpieza
    I1 --> C1
    I2 --> C1
    I3 --> C1
    I4 --> C1
    I5 --> C1

    %% Limpieza -> Modelo Semántico
    C1 --> S1c
    C1 --> S2c
    C1 --> S3c
    C1 --> S4c

    %% Modelo Semántico -> Productos de Datos
    S1c --> P1a
    S3c --> P2a
    S4c --> P3a
    S2c -.-> P3a  %% Cuenta conectada opcionalmente al Feature Store



