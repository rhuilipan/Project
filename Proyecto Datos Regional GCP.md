# Diseño de Plataforma de Datos Federada – Google Cloud Platform en Global Airline Group

### **Proyecto**: Plataforma Federada de Ingesta y Procesamiento de Datos
### **Fecha**: Abril 2025

## Índice Propuesto – Documento de Proyecto Plataforma Federada

1. [Contexto Inicial](#contexto_inicial)
    - 1.1 Fase de Diseño
    - 1.2 Sugerencia inicial: Alineación Estratégica
    - 1.3 Enfoque Estratégico y Modelo de Ejecución:
    - 1.4 Sugerencia inicial: Alineación Estratégica
    - 1.5 Fase de Diseño: Co-creación de la Arquitectura
    - 1.6 Fase de Ejecución: Enfoque Iterativo y Visible

2. [Alcance del Proyecto](#alcance-del-proyecto)
    - 2.1 Qué incluye y qué no incluye la solución
    - 2.2 Principales fuentes y sistemas involucrados
    - 2.3 Equipos y regiones afectadas

3. [Estrategia de Ejecución](#estrategia-de-ejecucion)
    - 3.1 Plan de reuniones iniciales y workshops de descubrimiento
    - 3.2 Metodología propuesta (Agile, milestones, etc.)
    - 3.3 Roles clave y conformación de equipos (ej. Data Council)

4. [Evaluación de Alternativas Técnicas](#evaluacion-de-alternativas-tecnicas)
    - 4.1 Análisis de opciones de arquitectura 
    - 4.2 Criterios de decisión: rendimiento, gobernanza, costos, escalabilidad
    - 4.3 Justificación de la solución elegida

5. [Arquitectura Propuesta](#arquitectura-propuesta)
    - 5.1 Diagrama general
    - 5.2 Flujo de ingesta y procesamiento
    - 5.3 Gobernanza, seguridad y control de calidad de datos
    - 5.4 Consulta federada y mecanismos de explotación

6. [Planificación de Esfuerzos](#planificacion-de-esfuerzos)
    - 6.1 Estimación de recursos por etapa (personas/roles/skills)
    - 6.2 Propuesta de staffing interno y externo
    - 6.3 Capacitaciones necesarias y ramp-up

7. [Evaluación Económica](#evaluación-economica)
    - 7.1 Costos por servicio GCP estimados
    - 7.2 Costos operativos y licencias (si aplica)
    - 7.3 Escenarios de optimización y control de gasto

8. [Plan de Entregables](#plan-de-entregables)
    - 8.1 Hitos principales (MVPs, pilotos, fases)
    - 8.2 Fechas estimadas de entrega por etapa
    - 8.3 Métricas de éxito y criterios de aceptación

9. [Riesgos y Estrategias de Mitigación](#eiesgos-y-estrategias-de-mitigacion)
    - 9.1 Riesgos técnicos y organizacionales
    - 9.2 Planes de contingencia y fallback
    - 9.3 Monitoreo y revisión periódica

10. [Conclusión y Siguientes Pasos](#conclusion-y-siguientes-pasos)
    - 10.1 Impacto esperado
    - 10.2 Propuesta de seguimiento y evolución continua
    - 10.3 Formalización de acuerdos e inicio de ejecución


## Contexto Inicial:
Con operaciones en múltiples regiones, nuestras unidades de negocio generan volúmenes significativos de datos diariamente, provenientes de sistemas locales diversos y arquitecturas regionalizadas. En este escenario, **la información es un activo crítico**, y la capacidad de integrarla y transformarla en insights accionables define nuestra ventaja competitiva.
Hoy, nuestra organización enfrenta el desafío de romper con los silos de datos que existen entre regiones. Cada una ha evolucionado con autonomía, creando infraestructuras distintas que dificultan la interoperabilidad, encarecen el análisis y limitan nuestra capacidad de actuar a escala global.
Desde la dirección ejecutiva se ha solicitado una **plataforma federada de datos** que permita una visión única del negocio, sin comprometer la autonomía local ni incurrir en costos excesivos. Este desafío implica visión, alineación cultural y excelencia operativa.

### Enfoque Estratégico y Modelo de Ejecución:
Más allá de diseñar una solución técnica, nuestra misión será construir una plataforma que escale con el negocio, inspire confianza y acelere la toma de decisiones en todas las áreas.

### Sugerencia inicial: Alineación Estratégica
- Iniciaremos con una **serie de workshops de descubrimiento regional**, donde participarán los equipos técnicos y de negocio de cada zona. Queremos entender las particularidades de cada operación: tipos de datos, necesidades analíticas, restricciones regulatorias, etc.
- A su vez, desarrollaremos un **mapa de madurez de datos**, identificando brechas y oportunidades por región, con el fin de construir una solución flexible y priorizada.


### Fase de Diseño: Co-creación de la Arquitectura
- Se conformará un **Data Platform Council**, compuesto por referentes técnicos y líderes de producto de cada región. Esto nos permitirá garantizar el buy-in desde el inicio y evitar soluciones "impuestas".
- Las decisiones clave (gobernanza, zonificación de datos, particionamiento regional, etc.) se discutirán en este foro con foco en sostenibilidad y escalabilidad.

### Fase de Ejecución: Enfoque Iterativo y Visible
- Ejecutaremos el proyecto bajo un **modelo de entrega incremental (Agile)**, donde cada sprint entregará valor tangible: pipelines funcionales, catálogos publicados, dashboards unificados, etc.
- Organizaremos **demos quincenales abiertas** para todo el equipo ejecutivo, asegurando visibilidad, transparencia y alineación continua.
- Además, habilitaremos un **entorno de "sandbox" para analistas clave**, donde podrán probar la plataforma antes de su despliegue oficial.


---

## Problemática

Actualmente, la organización enfrenta desafíos técnicos y operativos derivados de:

- **Silos de datos regionales** en múltiples proyectos GCP.
- **Esquemas inconsistentes** entre fuentes.
- Latencias elevadas en análisis multi-región.
- Costos crecientes por replicación manual o soluciones ad-hoc.
- Falta de trazabilidad y unificación del modelo de datos global.

---

## Solución Propuesta

Se propone una **arquitectura federada**, que permita ingestar, catalogar y procesar datos provenientes de BigQuery, Cloud Storage y CloudSQL en múltiples regiones de GCP, soportando flujos en **tiempo real y batch**, con herramientas nativas para gobernanza, eficiencia y análisis avanzado.

---

### Arquitectura General

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

Estructura recomendada del repositorio:

