# Análisis Estadístico y Segmentación de Clientes — ConnectaTel

## Objetivo del Proyecto
Evaluar el comportamiento real de consumo de los clientes de **ConnectaTel** (operaciones en México y Colombia) en servicios móviles de llamadas y mensajes hasta el año 2024. El análisis busca construir un perfil estadístico de uso, detectar valores atípicos (*outliers*) y segmentar a los usuarios por nivel de consumo y grupo etario para optimizar la oferta comercial, diseñar estrategias de retención y proponer oportunidades de migración de planes (*upselling*).

---

## Datasets Utilizados
El análisis integra tres fuentes de datos relacionales:

1. **`plans.csv`** *(2 registros, 8 columnas)*: Catálogo de planes comerciales (`Basico` y `Premium`), incluyendo tarifas mensuales (`usd_monthly_pay`), beneficios incluidos (`messages_included`, `gb_per_month`, `minutes_included`) y costos por excedente.
2. **`users_latam.csv`** *(4,000 registros, 8 columnas)*: Información demográfica y contractual de los clientes (`user_id`, `age`, `city`, `reg_date`, `plan`, `churn_date`).
3. **`usage.csv`** *(40,000 registros, 6 columnas)*: Historial transaccional de actividad móvil (`type`: llamadas y mensajes, `date`, `duration` en minutos y `length` en caracteres).

---

## Etapas del Análisis Realizadas

1. **Carga y Exploración Inicial:** Inspección de dimensiones (`.shape`), tipos de datos (`.info()`) y estructura general de las tres tablas.
2. **Diagnóstico de Calidad de Datos:**
   - Identificación de valores nulos estructurales (MAR) en `duration` (55.19%) y `length` (44.74%) según el tipo de evento (`call` vs. `text`), así como en `churn_date` (88.35% de clientes activos).
   - Detección de *sentinels* en `age` (`-999`) y `city` (`?`), además de fechas fuera de rango en `reg_date` (40 registros en 2026).
3. **Limpieza y Estandarización:**
   - Imputación de `-999` en `age` utilizando la mediana (48 años).
   - Reemplazo de `?` en `city` por valores nulos estándar (`pd.NA`) y conversión de fechas posteriores a 2024 a `pd.NaT`.
4. **Agregación y Estadística Descriptiva:** Construcción de la tabla maestra `user_profile` consolidando por usuario el total de mensajes (`cant_mensajes`), llamadas (`cant_llamadas`) y minutos acumulados (`cant_minutos_llamada`).
5. **Análisis Visual y Detección de Outliers:**
   - Evaluación de distribuciones mediante histogramas y curvas KDE segmentadas por plan (`Basico`: 64.88% vs. `Premium`: 35.12%).
   - Cálculo de límites superiores mediante el método del Rango Intercuartílico (**IQR**) y validación con *boxplots*, conservando los *outliers* superiores al representar patrones reales de consumo intensivo.
6. **Segmentación de Clientes:**
   - **Por nivel de uso (`grupo_uso`):** `Bajo uso`, `Uso medio` y `Alto uso`.
   - **Por grupo etario (`grupo_edad`):** `Joven` (< 30 años), `Adulto` (30–59 años) y `Adulto Mayor` (≥ 60 años).
7. **Insights Ejecutivos y Recomendaciones:** Traducción de hallazgos estadísticos en acciones comerciales de retención y migración hacia el plan Premium.

---

## Cómo Ejecutar el Notebook

1.Clona y abre este repositorio en tu equipo:

```bash
git clone https://github.com/nilton-edu-naranjo/telecom-analysis.git
cd telecom-analysis
```
2.Descarga los tres archivos de datos ('plans_csv', 'users_latam.csv', 'usage.csv') desde este repositorio y súbelos en el panel izquierdo de colab(ícono de carpeta -> **Subir al almacenamiento de sesión**).
3.En la celda **1.1 carga de datos**, ajusta las rutas para que apunten al directorio raíz de colab:
plans = pd.read_csv('/datasets/plans.csv')
users = pd.read_csv('/datasets/users_latam.csv')
usage = pd.read_csv('/datasets/usage.csv')
4.Ejecuta todo el análisis haciendo clic en Entorno de ejecución > Ejecutar todas

## Guía Breve de Reproducción

- **Dependencias:** Requiere Python 3.8 o superior con las librerías pandas, numpy, matplotlib y seaborn (ya preinstaladas en Google Colab).
- **Orden de ejecución:** Ejecuta las celdas secuencialmente de arriba hacia abajo sin omitir el **Paso 3 (Limpieza básica)**, ya que las métricas estadísticas, el cálculo de límites IQR y las segmentaciones dependen de la corrección previa de *sentinels* (-999, "?") y fechas fuera de rango.
- **Archivos requeridos:** Asegúrate de contar con los tres datasets originales ('plans.csv', 'users_latam.csv' y 'usage.csv') en el mismo directorio de trabajo antes de iniciar la ejecución.
