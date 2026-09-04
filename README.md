<!--
PENDIENTE: reemplazar la imagen de abajo por una captura real de la app de
Streamlit (assets/demo.png) — ver MovieTime/CreditFlow como referencia de formato.
Borrar este comentario antes de commitear.
-->

<div align="center">

# 🛒 Metric Mindset

**Sistema de scoring de intención de compra y motor de recomendaciones para e-commerce**, construido end-to-end: EDA → modelado calibrado → API → app interactiva → deploy.

![Python](https://img.shields.io/badge/Python-3776AB?style=flat-square&logo=python&logoColor=white)
![LightGBM](https://img.shields.io/badge/LightGBM-02569B?style=flat-square&logo=lightgbm&logoColor=white)
![FastAPI](https://img.shields.io/badge/FastAPI-009688?style=flat-square&logo=fastapi&logoColor=white)
![Streamlit](https://img.shields.io/badge/Streamlit-FF4B4B?style=flat-square&logo=streamlit&logoColor=white)
![MLflow](https://img.shields.io/badge/MLflow-0194E2?style=flat-square&logo=mlflow&logoColor=white)

[![Demo](https://img.shields.io/badge/Demo-Ver_en_vivo-2ea44f?style=flat-square&logo=streamlit&logoColor=white)](https://proyecto-final-henry-cgwmikv2ykhrmpytpuv8hy.streamlit.app/)

</div>

<br>

<div align="center">
  <img src="assets/demo.png" alt="Vista previa de la app de Metric Mindset" width="720">
</div>

<br>

## 📑 Contenido

- [El problema, en una escena](#-el-problema-en-una-escena)
- [Mi rol y aportes](#-mi-rol-y-aportes-juan-manuel--data-scientist)
- [Demo en vivo](#-demo-en-vivo)
- [Qué hace](#-qué-hace)
- [Instalación y ejecución](#-instalación-y-ejecución)
- [Cómo está construido](#-cómo-está-construido)
- [Recursos útiles](#-recursos-útiles)
- [Próximos pasos](#-próximos-pasos)
- [Licencia](#-licencia)
- [Equipo](#-equipo)

---

## 🎬 El problema, en una escena

Un usuario entra a una tienda online, navega ocho minutos, mira cinco productos, agrega uno al carrito... y se va sin comprar. Pasa en **8 de cada 10 sesiones**, y el negocio no se entera hasta que ya es tarde para intervenir.

**Metric Mindset** responde una pregunta simple: *¿podemos anticipar esa decisión mientras la sesión todavía está ocurriendo, y decirle al negocio qué hacer al respecto?*

Lo construí como proyecto final del bootcamp de Data Science de Henry porque quería llevar un modelo más allá del notebook: calibrado, con una API real, una demo usable y desplegado — no solo una métrica en un Jupyter Notebook. Ahí está la diferencia frente a un proyecto de clasificación típico: no se queda en "esta sesión tiene 83% de probabilidad de compra", la traduce en una **acción de negocio concreta** (cross-selling, retención o ninguna acción) mediante umbrales calibrados.

---

## 🙋 Mi rol y aportes (Juan Manuel — Data Scientist)

El equipo fue de 4 personas (ver [Equipo](#-equipo)); mi parte, según el historial real de commits del repo, cubrió el ciclo completo:

- **Datos → Modelo:** ingesta y validación (`data_loader.py`), EDA propio + consolidación con el de un compañero, pipeline de ETL, feature engineering de comportamiento de sesión.
- **Modelado:** el MVP del Sprint 1, y en el Sprint 2 el tuning con Optuna, tratamiento del desbalance (SMOTE / `class_weight`) y la calibración de probabilidades — la pieza de mayor impacto (bajó el Brier Score ~33%).
- **Motor de recomendaciones:** `asignar_accion()`, la lógica que convierte probabilidad en acción de negocio.
- **Productivización:** API completa en FastAPI, conexión de la app Streamlit al modelo real, CI con GitHub Actions, tracking con MLflow, y el despliegue (Render + Streamlit Cloud).
- **Testing y documentación:** smoke tests de la API y gran parte de este README.

Lo que más me dejó el proyecto: la mitad del trabajo de un modelo "bueno" no es el algoritmo, es lo que pasa después de entrenarlo — calibrarlo, exponerlo, y hacer que alguien sin conocimientos técnicos pueda usarlo.

---

## 🚀 Demo en vivo

- **App (Streamlit):** [proyecto-final-henry-cgwmikv2ykhrmpytpuv8hy.streamlit.app](https://proyecto-final-henry-cgwmikv2ykhrmpytpuv8hy.streamlit.app/)
- **API (FastAPI):** desplegada en Render (ver `render.yaml`)

> Corren en planes gratuitos y pueden tardar 30-60s en "despertar" tras inactividad.

---

## ✨ Qué hace

1. 📊 Analiza el comportamiento de una sesión (páginas vistas, tiempo, rebotes).
2. 🎯 Estima la probabilidad de compra (score 0-1) con un LightGBM calibrado.
3. ⚡ Recomienda una acción según umbrales de negocio:

| Probabilidad | Acción | Tasa real de compra observada |
|---|---|---:|
| > 70% | Cross-selling | 85,9% |
| 30% – 70% | Sin acción | 44,9% |
| < 30% | Retención | 4,9% |

**Resultado del modelo final** (LightGBM + Optuna + calibración sigmoid, sobre test set): ROC-AUC 0,938 · PR-AUC 0,756 · Precision@10% 80% (de cada 10 sesiones priorizadas, 8 terminan comprando).

---

## 🔧 Instalación y ejecución

```bash
git clone https://github.com/Juanma-Alvarado/MetricMindset.git
cd MetricMindset
python -m venv venv && source venv/bin/activate   # Windows: .\venv\Scripts\activate
pip install -r requirements.txt

uvicorn api.main:app --reload        # API en http://localhost:8000/docs
streamlit run demo_app.py            # App en http://localhost:8501
```

Los artefactos del modelo ya están versionados en el repo — no hace falta reentrenar para probar la API o la app.

---

## 🏗️ Cómo está construido

| Capa | Tecnologías |
|---|---|
| Datos & Análisis | Python, Pandas, NumPy, Power BI |
| Machine Learning | Scikit-Learn, LightGBM, XGBoost, CatBoost, Optuna, Imbalanced-learn |
| Backend | FastAPI, Pydantic, Uvicorn |
| Frontend | Streamlit |
| MLOps | MLflow, GitHub Actions, deploy en Render + Streamlit Cloud |

**Dataset:** *Online Shoppers Intention* (UCI Machine Learning Repository) — 12.330 sesiones, fuerte desbalance (84,5% no compra). Tras limpieza: 12.205 filas × 30 columnas, incluyendo features derivadas de navegación.

**La pieza clave del modelo** — calibrar las probabilidades para que los umbrales de negocio sean confiables:

```python
CalibratedClassifierCV(method="sigmoid", cv=3)
# Brier Score: 0.101 (sin calibrar) → 0.068 (calibrado)
```

```text
api/            → endpoints FastAPI (main, routes, schemas, inference)
src/            → data_loader, preprocessing, features, metrics, recommender
notebooks/      → EDA, ETL, feature engineering, modelado (MVP y avanzado)
data/           → raw, processed, models
tests/          → smoke tests de la API
demo_app.py     → app Streamlit
```

---

## 📚 Recursos útiles

- Dataset: *Online Shoppers Purchasing Intention* — UCI Machine Learning Repository
- [Documentación de Optuna](https://optuna.readthedocs.io/)
- [CalibratedClassifierCV — scikit-learn](https://scikit-learn.org/stable/modules/generated/sklearn.calibration.CalibratedClassifierCV.html)

## 🔮 Próximos pasos

Llevar el sistema a un entorno cloud productivo, A/B testing de las estrategias de intervención, y monitoreo de drift del modelo.

## 📄 Licencia

Proyecto desarrollado con fines académicos como parte del proyecto final de Ciencia de Datos de Henry.

## 👥 Equipo

| Integrante | Rol |
|---|---|
| Camila Conde | Team Lead / Product Owner & Scrum Master |
| Luis | Data Engineer & Analytics |
| **Juan Manuel** | Data Scientist |
| Lucas Bottino | Data Scientist |

Repo original del equipo: [lucasbottino9/Proyecto-Final-Henry](https://github.com/lucasbottino9/Proyecto-Final-Henry) — este repositorio es mi fork, con mi aporte documentado arriba.

---

**Metric Mindset** — de los datos a la decisión, de la intención a la conversión.
