# 🏗️ Arquitectura Integral de Datos para COVALTO  
### **Por: Alejandra Hincapié Garzón**  
**Gerente de Ingeniería de Datos e IA — Liderazgo técnico, estratégico y sistémico**

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
**Necesidad:** analistas quieren crear reportes propios sin depender de desarrolladores.  
**Problemas actuales:**  
- Solicitudes ad-hoc que generan retrasos  
- Diferencias de significado entre distintas fuentes  
- Datos dispersos en múltiples sistemas  
- Ausencia de un modelo de datos común  

---

### **2️⃣ Evaluación de Riesgo para Hipotecas**
**Necesidad:** modelos basados en características derivadas, no en datos crudos.  
**Requiere:**  
- Procesos confiables y repetibles  
- Almacenamiento de características gobernado y versionado  
- Trazabilidad completa  
- Procesamiento en lotes y casi en tiempo real  

---

### **3️⃣ Monitoreo de Fraude en Tiempo Real**
**Necesidad:** ingestión de datos en streaming y acceso a características usadas en riesgo.  
**Requiere:**  
- Baja latencia  
- Confiabilidad en el procesamiento  
- Procesamiento basado en eventos  
- Acceso rápido a características de distintas fuentes

---

# 🏛️ 2. Principios de Diseño

- **Datos como Producto:** cada conjunto de datos tiene responsable, documentación, acuerdos de servicio y contratos claros.  
- **Semántica compartida:** diccionario de datos y modelo común para evitar confusión.  
- **Arquitectura en capas:** cada capa tiene funciones bien definidas.  
- **Procesamiento en tiempo real y por lotes:** cada caso usa la estrategia más adecuada.  
- **Trazabilidad y gobernanza desde el diseño:** todos los datos son auditable y rastreables.  
- **Escalabilidad horizontal:** manejo eficiente de límites de APIs y paralelismo.  
- **Calidad de datos:** validaciones automáticas, alertas y métricas de desempeño.

---

# 🏗️ 3. Arquitectura Propuesta (Visión 360°)

---

## 🥇 **Capa 0 — Ingesta Unificada (Batch + Streaming + Captura de Cambios)**

### **Fuentes de Datos y Estrategias**

#### **A. Declaraciones Anuales de Impuestos (API XML limitada)**
- Control de ingestión con **regulación de velocidad** y programación distribuida  
- Uso de **colas de trabajo** para paralelizar sin exceder límites  
- Caché para evitar llamadas repetidas

#### **B. Transacciones de Tarjetas de Crédito**
- Ingesta desde API JSON y bases internas  
- **Captura de cambios (CDC):** solo se traen las filas nuevas o modificadas  
- Validaciones de calidad: integridad, consistencia y completitud  
- Auditoría de reconciliación con sistemas contables  

#### **C. Estados de Cuenta Bancarios (XML y PDFs/Imágenes)**
- XML → parseo estructurado con validación de esquema  
- PDF/Imagen → OCR y procesamiento de lenguaje para extraer datos relevantes  
- Inteligencia artificial para estandarizar campos dudosos  
- Reglas de calidad: completitud, consistencia y validez de tipos

---

### **Tecnologías sugeridas:**
- Mensajería en tiempo real: Kafka, Pub/Sub  
- Orquestación de procesos por lotes: Airflow, Databricks Jobs  
- Almacenamiento: Data Lake en GCS o S3, formatos Parquet o Delta  
- OCR: AWS Textract o GCP Document AI  
- Procesamiento de datos: Spark, Beam, Databricks  
- Captura de cambios: Debezium, Fivetran o Change Data Streams  

---

## 🥈 **Capa 1 — Normalización y Limpieza de Datos**

Objetivo: **eliminar ambigüedad y unificar la semántica de todos los datos**.

Incluye:

- Tipificación de datos  
- Estandarización de fechas, valores monetarios y identificadores  
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

La arquitectura garantiza **seguridad, auditabilidad y confianza**:

- Catálogo y diccionario de datos  
- Gestión de acceso basada en roles y cumplimiento regulatorio  
- Contratos de datos entre equipos  
- Monitoreo de procesos con métricas y alertas  
- Validaciones automáticas de calidad  
- Linaje completo desde origen hasta consumo  

🎯 **Beneficio:** datos confiables y rastreables para BI, riesgo y fraude.

---

# 🧠 5. Diagrama de Arquitectura

```mermaid
flowchart LR

subgraph Sources["🔹 Fuentes de Datos"]
A[Declaraciones Anuales XML]
B[Transacciones de Tarjetas JSON]
C[Bases de Datos Internas]
D[Estados de Cuenta XML]
E[PDFs e Imágenes en S3]
end

subgraph Ingestion["🟦 Capa 0 - Ingesta y CDC"]
A --> I1[Regulación + Colas de Trabajo]
B --> I2[Ingesta en Streaming + Captura de Cambios]
C --> I3[Extracción Batch + Reglas de Calidad + Captura de Cambios]
D --> I4[Parseo XML]
E --> I5[OCR y Extracción NLP]
end

subgraph Processing["🟩 Capa 1 - Limpieza y Normalización"]
I1 --> C1[Normalización + Validación de Calidad]
I2 --> C1
I3 --> C1
I4 --> C1
I5 --> C1
end

subgraph Semantic["🟨 Capa 2 - Modelo Semántico"]
C1 --> S1[Cliente]
C1 --> S2[Cuenta]
C1 --> S3[Transacción]
C1 --> S4[Métricas Financieras]
end

subgraph Products["🟧 Capa 3 - Productos de Datos"]
S1 --> BI[Visualización y BI]
S3 --> FR[Motor de Fraude en Tiempo Real]
S4 --> FS[Feature Store para Riesgo]
end
