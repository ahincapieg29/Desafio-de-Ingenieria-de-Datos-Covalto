# 🏗️ Arquitectura Integral de Datos para COVALTO  
### **Solución al High Level Problem — Por: Alejandra Hincapié Garzón**  
**Gerente de Ingeniería de Datos e IA — Liderazgo técnico, estratégico y sistémico**

---

## 🎯 Propósito de esta Propuesta

Diseñar una **arquitectura moderna, escalable y segura** que permita a COVALTO habilitar:

- Autonomía analítica para equipos de negocio  
- Feature engineering robusto para modelos de riesgo  
- Ingesta y monitoreo en tiempo real para fraude  
- Gobernanza, calidad y trazabilidad exigidas en un banco regulado  

La solución aborda simultáneamente **necesidades de negocio**, **restricciones operativas**, y **madurez técnica**, integrando visión 360° del ecosistema de datos.

---

# 🧩 1. Resumen de los Casos de Uso

### **1️⃣ Customer Behavior Data Visualization**
**Necesidad:** analistas quieren crear sus propios reportes sin depender de desarrolladores.  
**Dolores actuales:**  
- Pedidos ad-hoc  
- Confusión semántica  
- Fuentes heterogéneas  
- Falta de un modelo común  

---

### **2️⃣ Risk Assessment para Hipotecas**
**Necesidad:** modelos basados en features derivados, no datos crudos.  
**Requiere:**  
- Pipelines confiables  
- Feature store gobernado  
- Trazabilidad  
- Procesamiento batch y near-real-time  

---

### **3️⃣ Fraud Monitoring (Tiempo Real)**
**Necesidad:** ingesta streaming + acceso a features usados en riesgo.  
**Requiere:**  
- Bajas latencias  
- Confiabilidad  
- Procesamiento event-driven  
- Acceso a features transversales

---

# 🏛️ 2. Principios de Diseño

- **Data as a Product** — cada conjunto de datos tiene dueño, SLA, documentación y contratos.  
- **Semántica compartida** — diccionario + modelo común para mitigar ambigüedad.  
- **Arquitectura en capas** — separación clara de responsabilidades.  
- **Real-time + batch coexistentes** — cada caso usa su mejor patrón.  
- **Trazabilidad y gobernanza aplicadas desde el diseño.**  
- **Escalabilidad horizontal** — APIs externas con límites requieren paralelismo y control.  

---

# 🏗️ 3. Arquitectura Propuesta (Visión 360°)

---

## 🥇 **Capa 0 — Ingesta Unificada (Batch + Streaming)**

### **Data Sources y estrategias:**

#### **A. Annual Tax Returns (XML API — rate-limited, sin bulk)**
- Ingesta controlada con **throttling** y **scheduler distribuido**.  
- Uso de **work queues** para paralelizar sin romper límites.  
- Caching administrativo para evitar llamadas repetidas.

#### **B. Credit Card Transactions**
- **API JSON (streaming + bulk):** conexión a **streaming ingestion** (Pub/Sub / Kafka).  
- **Pg interna (data quality baja):**  
  - Perfilamiento  
  - Imputación  
  - Reconciliación  
  - Auditoría de integridad  

#### **C. Bank Statements (XML + PDFs/Imágenes en S3)**
- XML → parseo estructurado.  
- PDF/imagen → OCR + NLP para extraer entidades.  
- ML para estandarizar campos dudosos / manuscritos.

---

### **Tecnologías sugeridas:**

- **Streaming:** Kafka / PubSub  
- **Batch orchestration:** Airflow  
- **Data Lake:** GCS / S3 en formato Parquet  
- **OCR:** AWS Textract / GCP Document AI  
- **ETL/ELT:** Spark, Databricks, Beam  

---

## 🥈 **Capa 1 — Procesamiento Estándar y Normalización**

Objetivo: **eliminar ambigüedad y crear semántica única** para toda la organización.

Incluye:

- Tipificación  
- Estandarización de fechas, valores monetarios, IDs  
- Detección de duplicados  
- Validación contra reglas de negocio  
- Auditoría y linaje automático  

📌 **Resultado:** Tablas **Clean** con consistencia garantizada.

---

## 🥉 **Capa 2 — Modelo Semántico Empresarial ("Conformed Layer")**

Aquí se diseñan los modelos con significados únicos:

- Customer  
- Account  
- Transaction  
- Credit behavior  
- Derived financial metrics  

**Valor:**  
- Elimina polysemy  
- Facilita self-service  
- Aporta entendimiento estándar a analistas, riesgo y fraude

---

## 🏅 **Capa 3 — Data Products según Caso de Uso**

### **A. Self-Service BI / Customer Behavior**
- Semantic Layer (dbt + LookML / Cube.js / PowerBI datasets)  
- Exposición controlada por permisos  
- Diccionario de datos vivo  
- Campos normalizados y validados  

🎯 Los analistas crean reportes **sin depender del equipo de ingeniería**.

---

### **B. Risk Assessment Feature Store**
**Requiere:**

- Feature Store (Feast / Hopsworks / Vertex Feature Store)  
- Versionado de features  
- Tiempos de validez (point-in-time correctness)  
- Pipelines batch y streaming sincronizados  

🎯 Garantiza fairness, reproducibilidad y precisión en modelos hipotecarios.

---

### **C. Fraud Monitoring en Tiempo Real**
- Transformaciones en streaming  
- Enriquecimiento con Features de riesgo  
- Scores en línea + almacenamiento en cola  
- Detección basada en reglas + ML  

🎯 Velocidad y precisión para proteger la operación bancaria.

---

# 🛡️ 4. Gobernanza, Calidad y Confianza

Una arquitectura bancaria debe ser **segura, auditable y confiable**.

Incluye:

- Catálogo y diccionario de datos  
- Gestión de acceso: RBAC + fines regulatorios  
- Data Contracts entre squads  
- Monitoreo de pipelines (SLAs / SLIs / SLOs)  
- Validaciones automáticas (Great Expectations / Data Quality Rules)  

🎯 **Beneficio:** datos confiables para BI, riesgo y fraude.

---

# 🧠 5. Diagrama de Arquitectura (Mermaid)

```mermaid
flowchart LR

subgraph Sources["🔹 Data Sources"]
A[Annual Tax Returns XML API]
B[Credit Card JSON API]
C[Internal PG Databases]
D[Bank Statements XML]
E[PDFs e Imágenes en S3]
end

subgraph Ingestion["🟦 Capa 0 - Ingesta"]
A --> I1[Throttle + Queue Workers]
B --> I2[Streaming Ingestion]
C --> I3[Batch Extract + DQ Checks]
D --> I4[XML Parser]
E --> I5[OCR + NLP Extraction]
end

subgraph Processing["🟩 Capa 1 - Clean"]
I1 --> C1[Normalization]
I2 --> C1
I3 --> C1
I4 --> C1
I5 --> C1
end

subgraph Semantic["🟨 Capa 2 - Conformed Model"]
C1 --> S1[Customer]
C1 --> S2[Account]
C1 --> S3[Transaction]
C1 --> S4[Financial Metrics]
end

subgraph Products["🟧 Capa 3 - Data Products"]
S1 --> BI[Self-Service BI Layer]
S3 --> FR[Fraud Streaming Engine]
S4 --> FS[Feature Store (Risk)]
end

