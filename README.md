# Data Engineering with Notebooks
This repository contains the code for the *Data Engineering with Notebooks* Snowflake Quickstart.

### ➡️ For overview, prerequisites, and to learn more, complete this end-to-end tutorial [Data Engineering with Notebooks](https://quickstarts.snowflake.com/guide/data_engineering_with_notebooks/index.html?index=..%2F..index#0) on quickstarts.snowflake.com.

___
Here is an overview of what we'll build in this tutorial:

<img src="images/quickstart_overview.png" width=800px>


## ❄️ Flujo de Despliegue Avanzado: Snowflake CI/CD

Este *workflow* de GitHub Actions automatiza el despliegue y la orquestación de tu proyecto de **dbt** directamente dentro de **Snowflake**, aprovechando la integración Git de Snowflake.

El **`deploy_snowflake.yml`** actúa como un orquestador que le dice a Snowflake cuándo y cómo sincronizar y ejecutar el código.

---

### 🎯 Propósito del Pipeline

El objetivo es orquestar la sincronización de código y la ejecución de *scripts* SQL en Snowflake, asegurando que solo el código validado se ejecute en los esquemas correctos.

### ⚙️ Mecanismos de Despliegue Clave

| Característica | Propósito | Comando Central |
| :--- | :--- | :--- |
| **Activación Flexible** | Permite disparar la ejecución automáticamente (`push` a cualquier rama) o manualmente (*workflow_dispatch*). | `on: push` y `workflow_dispatch` |
| **Gobernanza de Esquemas** | Crea dinámicamente el esquema de destino (ej. `MAIN_SCHEMA` o `DEV_SCHEMA`) si no existe, asegurando el aislamiento del entorno. | `CREATE SCHEMA IF NOT EXISTS` |
| **Sincronización de Código** | Fuerza a la integración de Git de Snowflake a descargar el *commit* más reciente de la rama que está siendo desplegada. | `ALTER GIT REPOSITORY ... FETCH` |
| **Ejecución Remota** | Ejecuta un *script* de despliegue (`deploy_notebooks.sql`) almacenado *dentro* del repositorio sincronizado de Snowflake, pasándole variables dinámicas (rama y entorno). | `EXECUTE IMMEDIATE FROM @DEMO_GIT_REPO...` |

---

### 💻 El Flujo de Ejecución en Snowflake

1.  **Setup y Auth:** Configuración del entorno de GitHub Actions y carga de credenciales de `snowsql` usando Secrets.
2.  **Alineación Dinámica:** El script determina la **rama de origen** (`$BRANCH_NAME`) y el **esquema de destino** (`$TARGET_ENV`).
3.  **CI/Fetch:** Se ejecuta `ALTER GIT REPOSITORY ... FETCH` para actualizar el repositorio interno de Snowflake con el código más reciente.
4.  **CD/Run:** Se ejecuta el *script* SQL principal que está dentro del repositorio de Snowflake para materializar los modelos y finalizar el despliegue.

