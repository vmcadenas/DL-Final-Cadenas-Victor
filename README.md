# DL-Final-Cadenas-Victor
DL-Final-Apellido-Nombre
🏭 Predicción de Recuperación de Cobre y Molibdeno en Planta Concentradora
Proyecto Final – Curso de Deep Learning
Autor: [Tu Nombre Apellido]

📋 Descripción del Problema
En operaciones mineras de concentración por flotación, la recuperación metalúrgica es el indicador más crítico de eficiencia del proceso. Una caída en la recuperación de Cobre (Cu) o Molibdeno (Mo) genera pérdidas económicas directas.
Este proyecto implementa modelos de Deep Learning para predecir:

Rec Cu T → Recuperación total de Cobre (%)
Rec Mo T → Recuperación total de Molibdeno (%)

a partir de 48 variables operacionales registradas por guardia de trabajo.

🎯 Objetivo
Desarrollar un sistema predictivo que permita a los operadores:

Anticipar caídas en recuperación antes de que ocurran
Identificar las variables operacionales más influyentes
Evaluar el desempeño de cada guardia de forma objetiva
Reducir pérdidas económicas por recuperación sub-óptima


📊 Dataset
AtributoDetalleNombreBase de Datos Evaluación de GuardiasTipoSeries de tiempo (datos tabulares por guardia)Registros972 guardias de producciónFeatures48 variables operacionalesVariables objetivoRec Cu T, Rec Mo TPeríodo2008 en adelanteFuentePlanta concentradora de cobre y molibdeno
Variables principales: P80, tonelaje, Wi Op, Ley Cu, % Cu/Mo/Fe por línea (L1-L4), % CuOx, % Cu SCN, Ind -325, Grado Bulk, % Humd, TMH.

⚠️ El dataset no se incluye en el repositorio por confidencialidad. Cargar desde Google Drive según instrucciones del notebook.


🧠 Modelos Implementados
ModeloArquitecturaLossRegularizaciónMLP BaseDense(64→32) → OutputMSE—MLP ProfundoDense(256→128→64→32) + BN + DropoutHuberL2 + DropoutLSTMLSTM(128) → LSTM(64) → Dense(32) → OutputMSEDropout recurrenteMLP OptimizadoDense(512→256→128→64) + LeakyReLU + BN + DropoutHuberDropout
Justificación de arquitecturas:

MLP Base: Baseline de referencia para comparación objetiva
MLP Profundo: BatchNormalization acelera convergencia, Dropout previene overfitting, Loss Huber robusto a outliers
LSTM: Explota dependencias temporales entre guardias consecutivas mediante memoria de largo plazo
MLP Optimizado: Arquitectura más profunda con LeakyReLU para evitar neuronas muertas


📈 Pipeline
Dataset (.xls, 972 registros)
    │
    ▼
Data Preparation
├── Imputación de nulos con mediana
├── Tratamiento de outliers IQR (factor=3.0)
├── Split temporal 70% / 15% / 15%
└── Normalización StandardScaler (fit solo en train)
    │
    ▼
Modeling
├── MLP Base         → baseline
├── MLP Profundo     → mejor modelo ✅
├── LSTM             → modelo temporal
└── MLP Optimizado   → arquitectura extendida
    │
    ▼
Evaluation & Analysis
├── RMSE, MAE, R² por objetivo
├── Curvas de entrenamiento
├── Gráficos Real vs Predicho
├── Serie temporal de predicciones
└── Permutation Feature Importance
    │
    ▼
Mejor modelo guardado → mejor_modelo_recuperacion.h5

📊 Resultados
ModeloR² Rec Cu TRMSE Rec Cu TR² Rec Mo TRMSE Rec Mo TR² PromedioMLP Base-0.14911.830.10027.07-0.025MLP Profundo ✅0.5067.760.12126.750.313LSTM-1.24916.76-0.30833.05-0.779MLP Optimizado0.1919.920.17525.920.183
✅ Mejor modelo: MLP Profundo (R² promedio: 0.313)
Análisis de resultados

El MLP Profundo supera a todos los modelos con R²=0.51 para Cu
La predicción de Rec Mo T es intrínsecamente difícil por su alta variabilidad natural (rango de -60% a +85% en test)
El LSTM sufre overfitting con el tamaño actual del dataset (972 registros)
El MLP Optimizado mejora ligeramente Mo (0.17) pero no supera al MLP Profundo en Cu

Top 5 Features más importantes (Permutation Importance)
RankingFeatureImportanciaInterpretación1Rec Mo PM.10.222El proceso tiene memoria: Mo previo predice Mo actual2% CuOx L10.114Cobre oxidado en línea 1 → afecta selectividad de flotación3% Cu SCN L10.093Cobre soluble → indica mineralogía compleja4% Cu L40.087Ley de Cu en última línea → control de calidad final5% CuOx L30.083Cobre oxidado en línea 3 → afecta recuperación global
