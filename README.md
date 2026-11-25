# Análisis Estadístico y Segmentación de Clientes — ConnectaTel

## Objetivo del Proyecto
Evaluar el comportamiento real de consumo de los clientes de **ConnectaTel** (operaciones en México y Colombia) en servicios móviles de llamadas y mensajes hasta el año 2024. El análisis busca construir un perfil estadístico de uso, detectar valores atípicos (*outliers*) y segmentar a los usuarios por nivel de consumo y grupo etario para optimizar la oferta comercial, diseñar estrategias de retención y proponer oportunidades de migración de planes (*upselling*).

---

## Datasets Utilizados
El análisis integra tres fuentes de datos relacionales:

1. **`plans.csv`** *(2 registros, 8 columnas)*: Catálogo de planes comerciales ('Basico' y 'Premium'), incluyendo tarifas mensuales ('usd_monthly_pay'), beneficios incluidos ('messages_included', 'gb_per_month', 'minutes_included') y costos por excedente.
2. **`users_latam.csv`** *(4,000 registros, 8 columnas)*: Información demográfica y contractual de los clientes ('user_id', 'age', 'city', 'reg_date', 'plan', 'churn_date').
3. **`usage.csv`** *(40,000 registros, 6 columnas)*: Historial transaccional de actividad móvil ('type': llamadas y mensajes, 'date', 'duration' en minutos y `length` en caracteres).

---

## Etapas del Análisis Realizadas

1. **Carga y Exploración Inicial:** Inspección de dimensiones ('.shape'), tipos de datos ('.info()') y estructura general de las tres tablas.
2. **Diagnóstico de Calidad de Datos:**
   - Identificación de valores nulos estructurales (MAR) en 'duration' (55.19%) y 'length' (44.74%) según el tipo de evento ('call' vs. 'text'), así como en 'churn_date' (88.35% de clientes activos).
   - Detección de *sentinels* en 'age' (-999) y 'city' ("?"), además de fechas fuera de rango en `reg_date` (40 registros en 2026).
3. **Limpieza y Estandarización:**
   - Imputación de -999 en `age` utilizando la mediana (48 años).
   - Reemplazo de `?` en `city` por valores nulos estándar (`pd.NA`) y conversión de fechas posteriores a 2024 a `pd.NaT`.
4. **Agregación y Estadística Descriptiva:** Construcción de la tabla maestra 'user_profile' consolidando por usuario el total de mensajes ('cant_mensajes'), llamadas ('cant_llamadas') y minutos acumulados ('cant_minutos_llamada').
5. **Análisis Visual y Detección de Outliers:**
   - Evaluación de distribuciones mediante histogramas y curvas KDE segmentadas por plan ('Basico': 64.88% vs. 'Premium': 35.12%).
   - Cálculo de límites superiores mediante el método del Rango Intercuartílico (**IQR**) y validación con *boxplots*, conservando los *outliers* superiores al representar patrones reales de consumo intensivo.
6. **Segmentación de Clientes:**
   - **Por nivel de uso (grupo_uso):** 'Bajo uso', 'Uso medio' y 'Alto uso'.
   - **Por grupo etario (grupo_edad):** 'Joven' (< 30 años), 'Adulto' (30–59 años) y 'Adulto Mayor' (≥ 60 años).
7. **Insights Ejecutivos y Recomendaciones:** Traducción de hallazgos estadísticos en acciones comerciales de retención y migración hacia el plan Premium.

---

## Cómo Ejecutar el Notebook

1. **Clonar el repositorio y ubicar el proyecto:**

```bash
git clone https://github.com/nilton-edu-naranjo/telecom-analysis.git
cd telecom-analysis
```
2. Abrir el notebook en Google Colab:
Abre el archivo del notebook (por ejemplo, telecom_analysis.ipynb) en Google Colab (ya sea subiéndolo desde Archivo > Subir notebook o abriéndolo directamente desde GitHub) y sube los archivos 'plans.csv', 'users_latam.csv', 'usage.csv' desde el panel izquierdo (Archivos > Subir al almacenamiento de sesión).
3. Cargar los Datasets en Colab:
Al subir los archivos directamente en la sesión de Colab, quedan disponibles en el directorio de trabajo actual (/content/). Ejecuta la lectura usando sus nombres exactos:

```python
import pandas as pd
plans = pd.read_csv('plans.csv')
users = pd.read_csv('users_latam.csv')
usage = pd.read_csv('usage.csv')
```
4. Corre las celdas de arriba abajo en orden secuencial para reproducir la limpieza, el merge, el análisis de outliers, la segmentación y las visualizaciones.

## Guía Breve de Reproducción

Entorno recomendado: Python 3.8 o superior (compatible con Google Colab o Jupyter Notebook/Lab).
Librerías principales: pandas, numpy, matplotlib y seaborn.
Archivos requeridos: 'plans.csv', 'users_latam.csv', 'usage.csv' y el notebook (.ipynb) correspondiente.
Orden de ejecución: Respeta el orden secuencial de las celdas —especialmente en el paso de limpieza y consolidación de variables— para evitar inconsistencias en las métricas finales o en la detección de outliers.
