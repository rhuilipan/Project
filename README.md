# Diseño de Plataforma de Datos Federada – Google Cloud Platform en Global Airline Group

### **Proyecto**: Plataforma Federada de Ingesta y Procesamiento de Datos
### **Fecha**: Abril 2025

## Índice Propuesto – Documento de Proyecto Plataforma Federada

- [Introduccion](#introduccion)

- [Problemática](#problematica)

3. [Solución Propuesta](#solucion-propuesta)

4. [Diagrama Solución](#diagrama-solucion)
    
5. [Principales Preguntas](#principales-preguntas)
-------
##    Introduccion

El presente documento detalla la propuesta de diseño, planificación y ejecución para una plataforma federada de datos en Google Cloud Platform, adaptada al contexto de una organización global del sector aeronáutico. En la actualidad, los datos operativos, comerciales y de experiencia del cliente se encuentran dispersos en múltiples regiones, proyectos y sistemas. Esto dificulta la generación de métricas consistentes, retrasa la toma de decisiones y limita el uso avanzado de datos (machine learning, personalización, etc.). El objetivo del proyecto es diseñar una solución técnica y organizacional que habilite la integración de datos multirregión con un enfoque moderno, gobernado y orientado al autoservicio, utilizando capacidades nativas de GCP como BigQuery, Dataflow, Dataplex, Pub/Sub, y otros.

---

## Problemática

Actualmente, la organización enfrenta las siguentes problemáticas:

- **Silos de datos regionales** en múltiples proyectos GCP o fuera de estos.
- **Esquemas inconsistentes** entre fuentes.
- Latencias elevadas en análisis multi-región.
- Costos crecientes por replicación manual o soluciones ad-hoc.
- Falta de trazabilidad y unificación del modelo de datos global.

---

## Solución Propuesta
Previo al inicio de la solución técnica, es crucial comenzar con una serie de reuniones de exploración regional que faciliten comprender las características operativas y analíticas de cada región, junto con un mapa de madurez de datos que detecte vacíos, oportunidades y estructuras de esquemas. La arquitectura se desarrollará en colaboración con un **Data Platform Council**, compuesto por líderes técnicos regionales y representantes del proveedor de cloud, garantizando alineación, sostenibilidad y adopción anticipada. La implementación se llevará a cabo a través de un esquema iterativo e incremental, proporcionando un valor tangible en cada sprint y incentivando la visibilidad constante a través de demos quincenales y ambientes de prueba ("sandbox") para los usuarios claves, garantizando de esta manera un desarrollo orgánico y colaborativo del plan.

Se propone una **arquitectura federada**, que permita ingestar, catalogar y procesar datos provenientes de BigQuery, Cloud Storage y CloudSQL en múltiples regiones de GCP, soportando flujos en **tiempo real y batch**, con herramientas nativas para gobernanza, eficiencia y análisis avanzado.

---

### Diagrama Solución
La solución propuesta se visualiza en el siguiente diagrama:

### Arquitectura de la Plataforma Federada de Datos – GCP

#### Región A / Proyecto Regional
```
+-------------------------------------------------------------+
|                    Proyecto Regional A                      |
|                                                             |
|  +-----------+   +--------------+    +-----------+          |
|  | CloudSQL  |   | CloudStorage |    | BigQuery  |          |
|  +-----+-----+   +------+-------+    +-----+-----+          |
|        |                |                  |                |
|   +----v----+     +-----v------+     +-----v-----+          |
|   | Dataflow|     |  Pub/Sub   |     |EXPORT DATA|          |
|   +---------+     +------------+     +-----------+          |
|        |                  |                                 |
|        |             +----v------+                          |
|        +------------>|Cloud Func.|                          |
|                      +-----------+                          |
+-------------------------------------------------------------+
                          |
                          v
              (Federated query or shared staging)
```
Cada región ya posee su propio proyecto en GCP, donde funcionan los sistemas y fuentes de datos de la zona. Estas fuentes comprenden bases de datos PostgreSQL en CloudSQL, archivos en el Almacenamiento Cloud (GCS), y tablas analíticas propias en BigQuery. Desde ese punto, la captura de datos se realiza de manera híbrida: por un lado, los procesos batch con Dataflow se vinculan con CloudSQL para obtener datos de manera periódica; por otro, los sucesos de carga en GCS activan funciones en Cloud Functions, las cuales se publican en Pub/Sub y generan pipelines de consumo en tiempo real. Para BigQuery, se utilizan herramientas nativas como EXPORT DATA para exportar subconjuntos de tablas a una zona compartida, o se realiza una consulta directa a través de la federación. Cada componente regional mantiene autonomía y su propia frecuencia de actualización, pero aporta sus datos a la plataforma central sin alterar sus procesos locales.


#### Plataforma Central – Curación y Federación
```
+-------------------------------------------------------------+
|              Plataforma Central (analytics-core)            |
|                                                             |
|  +-----------------------+    +--------------------------+  |
|  | Curated BQ Datasets   |    | External Tables (BQ/SQL) |  |
|  +----------+------------+    +------------+-------------+  |
|             v                              v                |
|       +-------------+          +----------------------+     |
|       |Materialized |<-------->|   EXTERNAL_QUERY     |     |
|       |   Views     |          +----------------------+     |
|       +------+------+                                       |
|              v                                              |
|  +---------------------------+                              |
|  | BI Engine + Looker Studio|                               |
|  +---------------------------+                              |
+-------------------------------------------------------------+
```
El proyecto centralizado (por ejemplo, analytics-core) es el punto de convergencia donde se construye la visión global del negocio. Aquí se alojan los datasets armonizados en BigQuery, organizados por dominio de negocio (ej. rutas, pasajeros, reservas). Existen dos formas de obtener estos datos: la primera es a través de pipelines ETL que consolidan, transforman y cargan información desde los proyectos regionales hacia tablas curadas; la segunda es a través de consultas federadas (EXTERNAL_QUERY), que acceden directamente a los datos donde residen, sin necesidad de copiarlos. Para optimizar la experiencia de consumo, se construyen vistas materializadas que consolidan los indicadores más utilizados. Finalmente, las herramientas de visualización como Looker Studio acceden directamente a este entorno curado, apoyadas por BI Engine para acelerar tiempos de respuesta. Esta capa permite que los analistas trabajen con datos consistentes, trazables y de alto rendimiento.


#### Gobernanza, Metadatos, Seguridad y Orquestación
```
+-------------------------------------------------------------+
|                  Gobernanza y Control                       |
|  +------------+  +--------------+  +----------------------+ |
|  |  Dataplex  |  | Data Catalog |  | IAM / Monitoring     | |
|  | (zonas     |  | (metadatos)  |  | Auditoría / Roles    | |
|  |  raw/etc)  |  +--------------+  +----------------------+ |
+-------------------------------------------------------------+
+-------------------------------------------------------------+
|                    Automatización                           |
|  +------------+  +----------------+  +---------------------+|
|  | Cloud Build|  | Cloud Scheduler|  | Terraform (IaC)     ||
|  +------------+  +----------------+  +---------------------+|
+-------------------------------------------------------------+
```
Para asegurar control, cumplimiento y sostenibilidad en toda la plataforma, se establece una capa transversal de gobernanza y automatización. Dataplex organiza los datos en zonas lógicas (raw, curated, analytics), define dominios y aplica políticas automáticas según el ciclo de vida del dato. Junto con Data Catalog, permite clasificar activos con etiquetas personalizadas (como confidencialidad, dominio de negocio, calidad, región de origen) y mantener un catálogo único de metadatos accesible por todos los equipos. Por otro lado, IAM y Cloud Monitoring se utilizan para controlar accesos granulares, auditar el uso y monitorear errores o anomalías en los pipelines. Toda la infraestructura y lógica se gestiona con Terraform (IaC) para garantizar reproducibilidad y estandarización. Además, se implementan workflows programados con Cloud Scheduler y despliegues automatizados con Cloud Build, asegurando que los pipelines batch y vistas materializadas se actualicen con consistencia y sin intervención manual.

---
## Principales Preguntas:

### ¿Cómo se ingieren, catalogan y procesan los datos entre regiones?
    Los datos serán ingeridos desde fuentes regionales existentes (CloudSQL, GCS, BigQuery) utilizando pipelines batch con Dataflow y triggers en tiempo real con Pub/Sub + Cloud Functions. Los datos curados o armonizados se almacenan en BigQuery centralizado, mientras que los datos no replicados se consultan vía queries federadas. Todo lo ingestado se catalogará automáticamente con Data Catalog y se clasificará en zonas lógicas mediante Dataplex, permitiendo trazabilidad y control en toda la plataforma.

### ¿Cómo se gestiona la consistencia de los esquemas y el versionado?
    Se implementará un esquema de versionado centralizado en JSON Schema almacenado en Git, con validaciones automáticas en cada pipeline de Dataflow. Además, se aplicarán convenciones de nomenclatura y mapeo de campos por dominio, soportadas por una capa de transformación curada en BigQuery, garantizando que todos los datos cumplan con estructuras esperadas antes de ser expuestos para el análisis.

### ¿Cómo se optimiza el rendimiento y el costo de las consultas entre regiones?
    Se priorizará el uso de EXTERNAL_QUERY() selectivo para análisis exploratorios, y se construirán vistas materializadas para KPIs recurrentes o dashboards críticos, evitando escaneo de grandes volúmenes remotos. Además, se utilizará particionamiento, clustering y BI Engine para reducir el costo por TB procesado y mejorar la latencia de respuesta en consultas complejas y distribuidas.

### ¿Qué compensaciones (trade-offs) y estrategias de respaldo se consideran (por ejemplo: caching, vistas materializadas)?
    La principal compensación está entre latencia vs. duplicación de datos. Para mantener bajo costo y evitar redundancia, las consultas federadas con indicadas, pero cuando la performance sea crítica, las vistas materializadas o replicación selectiva serán mas adecuadas. Como fallback, se usarán zonas de staging en GCS y pipelines batch programados para sincronizar datasets clave cuando la federación no sea viable por volumen o SLA.
