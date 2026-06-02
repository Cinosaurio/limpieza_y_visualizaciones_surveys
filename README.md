# Limpieza y Visualizaciones — Santander Churn Analysis

Análisis de riesgo de churn para usuarios de Santander México, basado en datos de encuesta.
El objetivo es identificar qué usuarios tienen mayor probabilidad de retirarse de la institución
o migrar a una fintech, usando variables de comportamiento, experiencia digital y atracción competitiva.

---

## Estructura del repositorio

```
limpieza_y_visualizaciones_surveys/
│
├── notebooks/
│   └── final_santander_codigo.ipynb     # Notebook principal de análisis
│
├── data/
│   └── Encuesta Preferencia de Entidades Financieras (Respuestas) - Respuestas de formulario 1.csv    # Dataset procesado (requerido para ejecución)
│
├── requirements.txt                      # Dependencias del proyecto
└── README.md
```

---

## ¿Qué hace este proyecto?

### Etapa 1 — Limpieza de datos
- Normalización de columnas con nombres largos y acentos
- Limpieza de campos de selección múltiple (separados por coma) → listas de Python
- Unificación de nombres de bancos y fintechs con diccionarios de variantes
- Detección y corrección de valores inválidos (`na`, `n/a`, `x`, `-`, etc.)
- Reclasificación de fintechs mal ingresadas en el campo de banco principal
- Validación de escalas Likert (1–5) y categorización de confianza y dificultad

### Etapa 2 — Feature Engineering
Construcción de 10 variables numéricas de riesgo a partir de respuestas de texto:

| Feature | Variable fuente | Peso en churn |
|---|---|---|
| `switching_risk` | Razones para cambiar de banco | 30% |
| `fintech_risk` | Fintechs que utiliza actualmente | 20% |
| `rewards_risk` | Beneficios que más valora | 15% |
| `app_risk` | Frustraciones con la app | 10% |
| `security_risk` | Preocupaciones de seguridad | 10% |
| `age_risk` | Rango de edad | 5% |
| `multiapp_risk` | Número de apps financieras | 5% |
| `preference_risk` | Preferencia fintech vs tradicional | 5% |
| `ux_frustration_idx` | Interacción: dificultad × errores técnicos | — |
| `competitiveness_gap` | Interacción: rewards + switching financiero | — |

### Etapa 3 — Churn Risk Score

Fórmula de cálculo:

```
S_base     = Σ (feature_i × peso_i)
S_ajustado = S_base × (1 + 0.20 × es_detractor)
churn_score = (S_ajustado - S_min) / (S_max - S_min) × 100
```

Clasificación por umbrales:
-  **Riesgo Crítico:** score ≥ 70
-  **Riesgo Medio:** score 40–69
-  **Riesgo Bajo:** score < 40

### Etapa 4 — Modelos Predictivos
- **NPS estimado** via `Confianza_Institucion` (promotores − detractores)
- **Random Forest** (200 árboles, profundidad máx. 6) para probabilidad continua de churn
- **Regresión Logística Multinomial** para clasificación en 3 niveles de riesgo con coeficientes interpretables por clase

---

## Cómo ejecutar

### En Google Colab (recomendado)

1. Abre [Google Colab](https://colab.research.google.com)
2. Ve a `Archivo → Abrir notebook → GitHub`
3. Pega la URL de este repositorio y selecciona `final_santander_codigo.ipynb`
4. Al ejecutar la celda de carga, sube el archivo `Encuesta_Santander_Limpia.csv` cuando se solicite

### En local

```bash
# 1. Clonar el repositorio
git clone https://github.com/tu_usuario/limpieza_y_visualizaciones_santander.git
cd limpieza_y_visualizaciones_santander

# 2. Instalar dependencias
pip install -r requirements.txt

# 3. Abrir el notebook
jupyter notebook notebooks/final_santander_codigo.ipynb
```

> **Nota:** Al correr en local, reemplaza la celda de `files.upload()` de Google Colab por:
> ```python
> df = pd.read_csv('data/Encuesta_Santander_Limpia.csv')
> ```

---

## Requisitos

Ver `requirements.txt`. Principales dependencias:

- Python 3.9+
- pandas, numpy, matplotlib, seaborn
- scikit-learn
- wordcloud, plotly

---

## Resultados principales

- **NPS Santander estimado:** 71.1 (promotores superan a detractores)
- **Usuarios en Riesgo Crítico:** segmento 18–25 años con uso activo de Nu/Klar
- **Principal driver de churn:** `switching_risk` — usuarios que mencionan comisiones y mejor app como razones de cambio
- **Vulnerabilidad clave:** gap en cashback y tasas de rendimiento frente a fintechs

---

## Tecnologías

![Python](https://img.shields.io/badge/Python-3.9+-blue)
![Pandas](https://img.shields.io/badge/Pandas-2.0-green)
![Scikit--learn](https://img.shields.io/badge/scikit--learn-1.3-orange)
![Google Colab](https://img.shields.io/badge/Google-Colab-yellow)
