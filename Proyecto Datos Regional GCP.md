# Diseño de Plataforma de Datos Federada – Google Cloud Platform en Global Airline Group

### **Proyecto**: Plataforma Federada de Ingesta y Procesamiento de Datos
### **Fecha**: Abril 2025

## Índice Propuesto – Documento de Proyecto Plataforma Federada

1. [Introducción](#introduccion)

2. [Problemática](#problematica)

3. [Solución Propuesta](#solucion-propuesta)

4. [Diagrama Solución](#diagrama-solucion)
    
5. [Flujo de Datos](#flujo-de-datos)

6. [Servicios y Costos Estimados](#servicios-y-costos-estimados)

7. [Conclusión y Recomendaciones](#conclusion-y-recomendaciones)

## Introducción

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

Antes de empezar con la solución técnica, es indispensable iniciar con una serie de reuniones de descubrimiento regional que permitan entender las particularidades operativas y analíticas de cada zona, complementadas con un mapa de madurez de datos que identifique brechas, oportunidades y estructuras de esquemas. La arquitectura será co-creada en conjunto con un **Data Platform Council ***, conformado por líderes técnicos regionales y representantes del proveedor cloud, asegurando alineación, sostenibilidad y adopción temprana. La ejecución se realizará mediante un modelo iterativo e incremental, entregando valor tangible en cada sprint y promoviendo visibilidad continua a través de demos quincenales y entornos de prueba ("sandbox") para usuarios clave, garantizando así una evolución orgánica y colaborativa del proyecto.

Se propone una **arquitectura federada**, que permita ingestar, catalogar y procesar datos provenientes de BigQuery, Cloud Storage y CloudSQL en múltiples regiones de GCP, soportando flujos en **tiempo real y batch**, con herramientas nativas para gobernanza, eficiencia y análisis avanzado.

---

### Diagrama Solución
La solución propuesta se visualiza en el siguiente diagrama:



#### 🗺️ Arquitectura de la Plataforma Federada de Datos – GCP

### 🌍 Región A / Proyecto Regional
```
+-------------------------------------------------------------+
|                 GCP Project: Region A (ej. us-east1)        |
|                                                             |
|  +-----------+    +--------------+    +-----------+         |
|  | CloudSQL  |    | CloudStorage |    | BigQuery  |         |
|  +-----+-----+    +------+-------+    +-----+-----+         |
|        |                 |                  |               |
|   +----v----+     +------v------+     +-----v-----+         |
|   | Dataflow|     |   Pub/Sub   |     |EXPORT DATA|         |
|   +---------+     +-------------+     +-----------+         |
+-------------------------------------------------------------+
              |                          |
              v                          v
        (Transformación)         (Eventos disparadores)

```
### 🧬 Plataforma Central – Curación y Federación
```
+-------------------------------------------------------------+
|                GCP Proyecto Centralizado (shared/core)      |
|                                                             |
|  +-----------------------+    +--------------------------+  |
|  | Curated BQ Datasets   |    | External Tables (SQL/CS) |  |
|  +----------+------------+    +------------+-------------+  |
|             v                              v                |
|       +-------------+          +---------------------+      |
|       | Materialized|<-------->|  EXTERNAL_QUERY()   |      |
|       |   Views     |          +---------------------+      |
|       +------+------+
|              v
|  +--------------------------+
|  | BI Engine + Looker Studio|
|  +--------------------------+
+-------------------------------------------------------------+
```

### Gobernanza, Metadatos y Seguridad
```
+-------------------------------------------------------------+
|                  Gobernanza y Control                       |
|  +------------+  +--------------+  +----------------------+ |
|  |  Dataplex  |  | Data Catalog |  | IAM / Monitoring     | |
|  | (zonas     |  | (metadatos)  |  | Auditoría / Roles    | |
|  |  raw/etc)  |  +--------------+  +----------------------+ |
+-------------------------------------------------------------+
```

## Automatización y Orquestación
```
+-------------------------------------------------------------+
|                    Automatización                           |
|  +------------+  +----------------+  +---------------------+|
|  | Cloud Build|  | Cloud Scheduler|  | Terraform (IaC)     ||
|  +------------+  +----------------+  +---------------------+|
+-------------------------------------------------------------+
```


La solución incluye los siguientes componentes:
*: cloud ran es gratis es para hacer api rest chicas: es serverless.
*: flask 

- **Cloud Pub/Sub**: canal de ingestión de eventos para procesamiento en tiempo real.
- **Cloud Functions**: disparadores ligeros para automatización basada en eventos.
- **Cloud Dataflow**: procesamiento ETL/ELT en batch y streaming.
- **Cloud Storage**: zona de datos crudos y almacenamiento intermedio.
- **BigQuery**: motor principal de almacenamiento analítico y federación SQL.
- **BigQuery Omni** (opcional): consultas entre nubes o regiones remotas.
- **Dataplex**: gobernanza de datos y definición de zonas (raw, curado, analítico).
- **Data Catalog**: catálogo de metadatos y control de versiones de esquemas.
- **Looker / BI Engine**: visualización y análisis de alto rendimiento.

---

### Flujo de Datos

1. **Captura**
   - **CloudSQL (PostgreSQL)**: extraído con Dataflow vía JDBC.
   - **Cloud Storage**: eventos `OBJECT_FINALIZE` disparan funciones para ETL.
   - **BigQuery**: datasets regionales exportados con `EXPORT DATA`.

2. **Ingesta**
   - Archivos aterrizan en buckets por región/domino (`raw/region_x/...`).
   - Cloud Dataflow mueve datos a BigQuery y los transforma.

3. **Procesamiento**
   - Transformación y normalización de esquemas.
   - Aplicación de reglas de negocio (e.g., conversión de monedas, unificación de columnas).
   - Consolidación en datasets curados listos para análisis.

4. **Federación y Consumo**
   - Consultas SQL desde BigQuery entre regiones o proyectos.
   - Materialización de vistas para KPIs de alta demanda.
   - Dashboards en Looker / Data Studio con acceso controlado.

---

## Servicios y Costos Estimados

| Servicio              | Rol en la Arquitectura | Estimación Mensual (USD) |
|-----------------------|-------------------------|---------------------------|
| **Cloud Pub/Sub**     | Ingesta de eventos      | $10 – $20                 |
| **Cloud Functions**   | Disparadores            | $5 – $15                  |
| **Cloud Storage**     | 10 TB (landing/raw)     | $100 – $150               |
| **CloudSQL**          | PostgreSQL regional     | $200 – $500               |
| **Cloud Dataflow**    | Procesamiento diario    | $500 – $1,200             |
| **BigQuery**          | 10 TB almacenados + 5 TB consultados | $400 – $800 |
| **BigQuery Omni**     | Opcional (multi-cloud)  | $100 – $300               |
| **Dataplex / Catalog**| Gobernanza, metadatos   | Sin costo directo         |
| **Looker / Studio**   | BI y dashboards         | Incluido (según plan)     |

> ⚠️ Los costos varían según el volumen de datos, frecuencia de consulta, y ubicación regional. Se recomienda aplicar prácticas como **particionado, clustering y uso de vistas materializadas** para optimizar.

---

## Gobernanza y Consistencia

- **Dataplex** se usará para definir zonas de datos: `raw`, `curated`, `analytics`.
- **Data Catalog** permite clasificar y versionar esquemas.
- Los pipelines incorporan validación de esquema antes de cargar datos.
- Uso de JSON schemas versionados con Git para asegurar integridad en transformaciones.

---

## Estrategias de Optimización
- **Vistas materializadas** para reducir consultas interregionales costosas.
- **BI Engine** para acelerar dashboards con caché en memoria.
- **Job scheduling** de agregaciones pesadas en horarios de baja demanda.
- **Dataflow autoscaling** para adaptarse al volumen dinámico.

---

## Riesgos y Mitigaciones

| Riesgo                        | Mitigación                                   |
|------------------------------|----------------------------------------------|
| Latencia entre regiones      | Caching + vistas materializadas              |
| Cambios en esquemas fuente   | Validación automática + versionado de esquema |
| Costos de escaneo elevado    | Particionado + Clustering + vistas agregadas |
| Fallos en pipelines          | Alertas + retries + DLQ con Pub/Sub          |

---

## Recomendaciones
- Establecer un proyecto “core” para almacenamiento y consumo central.
- Aplicar IaC (Terraform) para replicabilidad y control de cambios.
- Establecer prácticas DevOps/DataOps para despliegue y monitoreo.
- Monitorear adopción y calidad de datos con herramientas de lineage.

---

## Conclusión

Esta arquitectura federada permite a la compañía superar sus limitaciones actuales de integración, disponibilidad y análisis de datos. Gracias a servicios nativos de GCP, se logra una solución moderna, segura, escalable y lista para habilitar tanto analítica descriptiva como casos de uso avanzados de inteligencia artificial y machine learning.

---

## Repositorio y Entrega

El código, diagramas y documentación se encuentran en el siguiente repositorio público:  
🔗 [https://github.com/juanperez/latam-challenge](https://github.com/juanperez/latam-challenge)
