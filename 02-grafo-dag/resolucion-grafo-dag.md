🧭 Resolución Estratégica del Desafío de Grafo Dirigido Acíclico (DAG)

Por: Alejandra Hincapié Garzón
Cargo: Gerente de Ingeniería de Datos e IA — Liderazgo técnico, sistémico y estratégico

🌟 Contexto del Ejercicio

Este notebook aborda el Desafío de Ingeniería de Datos – Problema de Bajo Nivel de Covalto, centrado en la identificación de nodos críticos y rutas estratégicas dentro de un DAG con aristas ponderadas.

El enfoque combina:

📌 Análisis de caminos y centralidad para identificar nodos estratégicos.

📌 Visualización clara para stakeholders técnicos y de negocio.

📌 Documentación profesional y reproducible, fomentando buenas prácticas de ingeniería de datos.

🔹 Nota: Se utiliza NetworkX para análisis de grafos y Matplotlib para visualización.

🎯 Objetivos Estratégicos

🧠 Analizar exhaustivamente todos los caminos desde la fuente para detectar puntos críticos de flujo.

🔍 Identificar nodos con mayor alcance y centralidad que impactan decisiones de negocio y analítica avanzada.

⚙️ Evaluar la inserción de nuevos nodos y su efecto en la estructura del DAG, considerando condiciones de exclusividad y trade-offs.

📊 Comunicar insights estratégicos mediante visualizaciones precisas, promoviendo claridad y trazabilidad.

💡 Fomentar buenas prácticas de ingeniería de datos, incluyendo pruebas, trazabilidad, modularidad y documentación profesional.

🧩 Contenido del Notebook
Paso	Descripción
🔹 Paso 0	Importación de librerías y configuración inicial
🔹 Paso 1	Construcción y visualización del DAG original, destacando la fuente 0
🔹 Paso 2	Identificación de todos los caminos posibles desde la fuente 0
🔹 Paso 3	Conteo de caminos por nodo para determinar el nodo más alcanzable
🔹 Paso 4	Filtrado y ordenamiento de caminos hacia el nodo más alcanzable por costo descendente
🔹 Paso 5	Evaluación de inserción de nuevo nodo V', respetando condiciones de exclusividad
🔹 Paso 6	Visualización final del DAG con caminos críticos y nodo V' destacado
🔹 Paso 7	Resumen de hallazgos, implicaciones estratégicas y recomendaciones para negocio y analítica
📌 Ejecución Recomendada

Abrir el notebook analisis_dag_covalto.ipynb en Jupyter o VSCode.

Ejecutar las celdas en orden, asegurando reproducibilidad.

Revisar la sección final de hallazgos y conclusiones estratégicas, que conecta análisis técnico con decisiones de negocio.

Utilizar el notebook como referencia para simulaciones futuras sobre DAGs críticos en producción o entornos de prueba.
