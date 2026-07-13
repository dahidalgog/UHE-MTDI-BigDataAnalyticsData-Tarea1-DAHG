# Predicción de la prioridad de tickets de soporte técnico (B2B) mediante Machine Learning supervisado

Repositorio de la Tarea 1 del módulo **MTDI202: Big Data, Analytics & Data Scientist** — Maestría en Tecnologías de Información, mención Transformación Digital e Innovación, Universidad Hemisferios.

**Autor:** David Alejandro Hidalgo Guerra
**Correo:** dahidalgog@estudiantes.uhemisferios.edu.ec
**Docente:** Ing. Carlos Wladimir Carrillo Villavicencio MSc. TIC.

---

## Objetivo del repositorio

Este repositorio documenta el desarrollo completo de un análisis exploratorio de datos (EDA), visualización y un modelo básico de Machine Learning supervisado, aplicado a un dataset de tickets de soporte técnico en un entorno B2B. El objetivo es responder a la siguiente problemática:

> ¿Es posible predecir la prioridad de un ticket de soporte técnico (baja, media o alta) a partir de las características del incidente y del cliente afectado, de modo que el equipo de soporte pueda anticipar la asignación de recursos antes de completar la clasificación manual?

## Dataset

- **Nombre:** Support Ticket Priority Dataset (50K)
- **Fuente:** [Kaggle](https://www.kaggle.com/datasets/albertobircoci/support-ticket-priority-dataset-50k), autor `albertobircoci`
- **Registros:** 50,000 filas × 33 columnas
- **Dominio:** Gestión de servicios de TI (ITSM) / soporte técnico B2B multi-empresa y multi-región
- **Sensibilidad:** Dataset público, sin información personal identificable ni datos sensibles de clientes reales — se incluye directamente en `/data` sin necesidad de anonimización
- **Variable objetivo:** `priority` (low / medium / high), con distribución 50% / 35% / 15%
- **Limitación conocida:** el dataset no incluye una variable de fecha real; se utilizó `day_of_week` como proxy parcial de análisis temporal

## Metodología

El análisis siguió un pipeline secuencial de 7 etapas, desarrollado íntegramente en Python sobre Google Colab:

1. **Carga y lectura** del dataset (pandas)
2. **Revisión de estructura**: shape, tipos de dato, estadística descriptiva inicial
3. **Limpieza y preparación**: imputación de 906 nulos en `customer_sentiment` (1.8%) como `"unknown"`; verificación de 0 duplicados; exclusión de `ticket_id` y `company_id` como predictores (identificador sin valor predictivo y riesgo de fuga de información, respectivamente)
4. **Análisis exploratorio**: estadística agrupada, tablas cruzadas (`crosstab`, `pivot_table`), correlaciones, filtrado (`query`, `loc`)
5. **Visualización**: 11 gráficos (Plotly, Seaborn, Matplotlib) para variables numéricas, categóricas y el proxy temporal disponible
6. **Machine Learning básico**: clasificación multiclase con `RandomForestClassifier` (modelo principal) y `LogisticRegression` con estandarización (baseline), sobre una partición 80/20 estratificada
7. **Interpretación**: matriz de confusión, importancia de variables, hallazgos, limitaciones y recomendaciones

## Principales resultados

| Hallazgo | Evidencia |
|---|---|
| La prioridad tiene una base predecible, no arbitraria | Correlación de hasta 0.55 entre `org_users` y `priority_cat`; incremento monótono de variables de impacto entre niveles |
| Un incidente de seguridad es el disparador más fuerte | Eleva la probabilidad de prioridad "high" de 15% a 61% |
| `fintech` concentra el mayor riesgo; `gaming` la menor frecuencia pero mayor severidad puntual | 36% de tickets "high" en fintech; `gaming` con downtime promedio más alto (43.4 min) en sus tickets críticos |
| Random Forest supera claramente al baseline lineal | F1-macro de 0.922 vs. 0.825 (Regresión Logística) |
| El modelo respeta el orden ordinal de la prioridad | 0 confusiones directas entre "low" y "high" en la matriz de confusión |
| La disponibilidad de runbook muestra causalidad inversa | Tickets con runbook documentado tienen prioridad promedio más alta (1.74 vs. 1.53) — se documenta lo que ya se sabe crítico, no al revés |

**Métricas del modelo principal (Random Forest):** accuracy 93.3%, F1-macro 0.922. Limitación relevante: recall de 0.84 en la clase "high" (16% de tickets críticos no detectados).

## Estructura del repositorio

```
├── notebooks/
│   └── Tarea1_EDA_ML_SupportTickets_v2.ipynb   # Notebook completo (EDA + visualización + ML)
├── data/
│   └── Support_tickets.csv                      # Dataset público utilizado
├── outputs/
│   └── figuras/                                  # Gráficos exportados usados en el informe
└── README.md
```

## Cómo ejecutar el notebook

1. Clonar o descargar este repositorio.
2. Abrir `notebooks/Tarea1_EDA_ML_SupportTickets_v2.ipynb` en [Google Colab](https://colab.research.google.com/) o Jupyter.
3. Si el archivo `Support_tickets.csv` no se carga automáticamente, subirlo manualmente desde `/data` usando el panel lateral de archivos de Colab, o montar Google Drive.
4. Ejecutar las celdas en orden secuencial (Runtime → Run all / Entorno de ejecución → Ejecutar todas).
5. Las librerías requeridas (`pandas`, `numpy`, `plotly`, `matplotlib`, `seaborn`, `scikit-learn`) ya están disponibles por defecto en Google Colab.

## Informe formal

El informe completo en formato Word/PDF, con la interpretación detallada de cada resultado, se encuentra adjunto en la entrega de Moodle (fuera de este repositorio, según lo indicado en las instrucciones de la tarea).

## Licencia y uso

Repositorio de uso académico, desarrollado como entregable individual para el módulo MTDI202. El dataset utilizado es de acceso público bajo los términos de Kaggle; ver la fuente original para más detalles.
