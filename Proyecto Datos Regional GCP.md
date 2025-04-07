# Diseño de Plataforma de Datos Federada – Google Cloud Platform en Global Airline Group

### **Proyecto**: Plataforma Federada de Ingesta y Procesamiento de Datos
### **Fecha**: Abril 2025

## Índice Propuesto – Documento de Proyecto Plataforma Federada
1. [Introducción](#introduccion)
    - 1.1 Contexto general del negocio
    - 1.2 Motivación y problemática actual
    - 1.3 Objetivo del proyecto

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


## 1. Introducción

Este documento presenta una solución integral para construir una **plataforma de datos federada sobre Google Cloud Platform (GCP)**. Esta plataforma tiene como fin integrar y armonizar datos distribuidos geográficamente en distintas regiones y proyectos de GCP, permitiendo análisis corporativos eficientes sobre indicadores clave como rentabilidad de rutas, eficiencia de combustible y satisfacción del cliente.

El objetivo es maximizar el **valor del dato** con una arquitectura escalable, gobernada y optimizada en costos, mediante servicios administrados de GCP, manteniendo alta disponibilidad y rendimiento en operaciones interregionales.




---

## 2. Problemática

Actualmente, la organización enfrenta desafíos técnicos y operativos derivados de:

- **Silos de datos regionales** en múltiples proyectos GCP.
- **Esquemas inconsistentes** entre fuentes.
- Latencias elevadas en análisis multi-región.
- Costos crecientes por replicación manual o soluciones ad-hoc.
- Falta de trazabilidad y unificación del modelo de datos global.

---

## 3. Solución Propuesta

Se propone una **arquitectura federada**, que permita ingestar, catalogar y procesar datos provenientes de BigQuery, Cloud Storage y CloudSQL en múltiples regiones de GCP, soportando flujos en **tiempo real y batch**, con herramientas nativas para gobernanza, eficiencia y análisis avanzado.

---

### 3.1 Arquitectura General

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

### 3.2 Flujo de Datos

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

## 4. Servicios y Costos Estimados

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

## 5. Gobernanza y Consistencia

- **Dataplex** se usará para definir zonas de datos: `raw`, `curated`, `analytics`.
- **Data Catalog** permite clasificar y versionar esquemas.
- Los pipelines incorporan validación de esquema antes de cargar datos.
- Uso de JSON schemas versionados con Git para asegurar integridad en transformaciones.

---

## 6. Estrategias de Optimización

- **Vistas materializadas** para reducir consultas interregionales costosas.
- **BI Engine** para acelerar dashboards con caché en memoria.
- **Job scheduling** de agregaciones pesadas en horarios de baja demanda.
- **Dataflow autoscaling** para adaptarse al volumen dinámico.

---

## 7. Riesgos y Mitigaciones

| Riesgo                        | Mitigación                                   |
|------------------------------|----------------------------------------------|
| Latencia entre regiones      | Caching + vistas materializadas              |
| Cambios en esquemas fuente   | Validación automática + versionado de esquema |
| Costos de escaneo elevado    | Particionado + Clustering + vistas agregadas |
| Fallos en pipelines          | Alertas + retries + DLQ con Pub/Sub          |

---

## 8. Recomendaciones

- Establecer un proyecto “core” para almacenamiento y consumo central.
- Aplicar IaC (Terraform) para replicabilidad y control de cambios.
- Establecer prácticas DevOps/DataOps para despliegue y monitoreo.
- Monitorear adopción y calidad de datos con herramientas de lineage.

---

## 9. Conclusión

Esta arquitectura federada permite a la compañía superar sus limitaciones actuales de integración, disponibilidad y análisis de datos. Gracias a servicios nativos de GCP, se logra una solución moderna, segura, escalable y lista para habilitar tanto analítica descriptiva como casos de uso avanzados de inteligencia artificial y machine learning.

---

## 10. Repositorio y Entrega

El código, diagramas y documentación se encuentran en el siguiente repositorio público:  
🔗 [https://github.com/juanperez/latam-challenge](https://github.com/juanperez/latam-challenge)

Estructura recomendada del repositorio:

