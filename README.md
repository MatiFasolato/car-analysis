# 🚗 Segmentación de Vehículos: Análisis Multivariado con PCA y Clustering

Trabajo Práctico de Análisis Multivariado y Descubrimiento de Patrones, aplicado a la identificación de perfiles vehiculares en el mercado automotor.

---

## 📌 Descripción

Este proyecto aplica técnicas de análisis multivariado sobre un dataset de automóviles con especificaciones técnicas, comerciales y categóricas. El objetivo es reducir la complejidad del problema mediante **Análisis de Componentes Principales (PCA)** y, a partir de esa estructura latente, identificar **segmentos de mercado homogéneos** mediante técnicas de **clustering**, integrando tanto variables numéricas (precio, potencia, peso, rendimiento) como categóricas (tipo de vehículo, tracción, región de origen).

## 🎯 Objetivos

- Reducir la dimensionalidad del dataset mediante PCA, condensando la variabilidad de las variables técnicas y comerciales en un número menor de componentes interpretables.
- Identificar perfiles vehiculares homogéneos (arquetipos de mercado) mediante clustering sobre datos mixtos.
- Detectar patrones de ingeniería, eficiencia y rendimiento, y analizar cómo se relacionan con la configuración mecánica (tracción) y el origen (región).
- Aislar y comprender los valores atípicos (outliers) que representan configuraciones vehiculares extremas (alta performance, hiper-eficiencia, volumen utilitario).

## 📊 Dataset y variables

- **Registros:** 428 vehículos.
- **Variables originales:** 15, en inglés y unidades imperiales (USD, libras, pulgadas, millas por galón).
- **Variables utilizadas tras el preprocesamiento:** 14.

**Diccionario de datos**

| Variable | Original | Tipo | Descripción |
|---|---|---|---|
| `marca` | Brand | Categórica | Marca fabricante del automóvil. |
| `modelo` | Model | Categórica | Nombre específico del modelo. |
| `tipo_vehiculo` | VehicleClass | Categórica | Segmento/carrocería (Sedán, SUV, Deportivo, etc.). |
| `region_origen` | Region | Categórica | Región geográfica de fabricación. |
| `traccion` | DriveTrain | Categórica | Sistema de transmisión de potencia (delantera, trasera, integral). |
| `precio_venta` | MSRP | Numérica continua | Precio minorista sugerido (USD). |
| `motor_litros` | EngineSize | Numérica continua | Cilindrada del motor (litros). |
| `cilindros` | Cylinders | Numérica discreta | Cantidad de cilindros del motor. |
| `caballos_fuerza` | HorsePower | Numérica continua | Potencia máxima del motor. |
| `rendimiento_ciudad_kmL` | MPG_City | Numérica continua | Eficiencia en ciudad (km/L). |
| `rendimiento_ruta_kmL` | MPG_Highway | Numérica continua | Eficiencia en ruta (km/L). |
| `peso_kg` | Weight | Numérica continua | Peso total del vehículo (kg). |
| `distancia_ejes_cm` | Wheelbase | Numérica continua | Distancia entre ejes (cm). |
| `largo_cm` | Length | Numérica continua | Longitud total del vehículo (cm). |

## 🔬 Metodología

1. **Pre-procesamiento:** eliminación de `DealerCost` por colinealidad perfecta con `precio_venta`; conversión de unidades del sistema imperial al Sistema Métrico Legal Argentino (SIMELA); limpieza de caracteres especiales en variables económicas; renombrado de variables al español; imputación fundamentada de valores faltantes en `cilindros` (motor rotativo Mazda RX-8).
2. **Análisis exploratorio (EDA):** análisis univariado de variables numéricas y categóricas, matriz de dispersión y matriz de correlación de Pearson.
3. **PCA:** estandarización de variables (`StandardScaler`), selección del número de componentes (autovalores > 1, varianza acumulada, criterio del codo), interpretación de *loadings* y contribuciones, proyección de individuos (*score plot*) e identificación de outliers.
4. **Clustering sobre datos mixtos:** cálculo de la **matriz de distancia de Gower** (variables numéricas y categóricas combinadas), evaluación de métodos de encadenamiento jerárquico (simple, completo, promedio, Ward) mediante el **coeficiente cofenético**, selección del número óptimo de clústeres (Silhouette Score + inspección del dendrograma + criterio de negocio) y *profiling* comercial de los segmentos resultantes.

## 📈 EDA — Hallazgos principales

- La mayoría de los vehículos se concentra en precios bajos/medios (media: USD 32.775; mediana: USD 27.635), con outliers de hasta ~USD 190.000.
- Fuerte correlación positiva entre `motor_litros`, `caballos_fuerza` y `peso_kg` (relación física directa tamaño-potencia).
- Correlación negativa entre el rendimiento de combustible y las variables de tamaño/potencia (p. ej. `rendimiento_ciudad_kmL` vs. `peso_kg`: r = -0,74).
- Distribución de vehículos: 61,2 % Sedán, seguido por SUV, Sports, Wagon, Truck e Hybrid (minoritario).
- Origen: Asia (36,9 %), USA (34,4 %) y Europa (28,7 %), sin una categoría dominante clara.
- Tracción delantera predominante (52,8 %), seguida de trasera (25,7 %) e integral (21,5 %).

## 🧩 PCA — Resultados

Se seleccionaron **9 variables numéricas** (precio, capacidad mecánica, eficiencia y estructura física) para el análisis, dado que presentaban correlaciones fuertes que justifican la reducción dimensional.

- **2 componentes principales** retenidos (criterio de autovalor > 1, ~83 % de varianza acumulada).
- **CP1 — "Tamaño y Potencia vs. Eficiencia" (67,3 % de varianza):** opone motor, cilindros, potencia y peso a los rendimientos de ciudad y ruta. Es la dimensión técnica del vehículo.
- **CP2 — "Exclusividad y Deportividad vs. Volumen Familiar" (15,5 % de varianza):** opone precio y potencia (deportivos/premium) a distancia entre ejes y largo (utilitarios/familiares).
- **Outliers identificados:** Porsche 911 GT2 (extremo de exclusividad/deportividad), Ford Excursion 6.8 XLT (extremo de tamaño y masa) y Honda Insight 2dr (extremo de eficiencia).

## 🧬 Clustering — Resultados

- **Métrica utilizada:** Distancia de Gower (apta para variables mixtas: `precio_venta`, `caballos_fuerza`, `rendimiento_ciudad_kmL`, `peso_kg`, `tipo_vehiculo`, `traccion`, `region_origen`).
- **Método de encadenamiento seleccionado:** Promedio (Average Linkage), con **coeficiente cofenético = 0,73** (el más alto entre simple, completo, promedio y Ward).
- **Número de clústeres:** 6. La elección se realizó integrando el Silhouette Score, la inspección del dendrograma y, especialmente, la interpretabilidad y aplicabilidad comercial de los segmentos, evitando una sobre-segmentación del mercado.

**Segmentos identificados:**

| Clúster | Perfil | Vehículos | Precio medio | Rasgos clave |
|---|---|---|---|---|
| 1 | Premium Europeo | 88 | USD 54.200 | Tracción trasera, alta potencia (270 HP), exclusividad |
| 2 | Sedanes de Potencia Americana | 41 | USD 31.700 | Tracción trasera, motores potentes (238 HP) |
| 3 | Utilitarios/SUVs Pesadas Americanas | 33 | USD 34.500 | Tracción integral, mayor peso (2.135 kg), menor eficiencia |
| 4 | SUVs Multipropósito Asiáticas | 70 | USD 30.700 | Tracción integral, equilibrio precio/potencia/peso |
| 5 | Anomalía Híbrida | 3 | USD 19.920 | Máxima eficiencia (23,4 km/L), mínimo peso y potencia |
| 6 | Mercado Generalista Asiático | 193 | USD 23.800 | Tracción delantera, segmento masivo y accesible |


## 🛠️ Tecnologías

- **Lenguaje:** Python
- **Entorno:** Jupyter Notebook
- **Librerías principales:**
  - `pandas`, `numpy` — manipulación de datos
  - `matplotlib`, `seaborn` — visualización
  - `scikit-learn` (`StandardScaler`, `PCA`, `silhouette_score`) — modelado y evaluación
  - `scipy` (`cluster.hierarchy`, `spatial.distance`) — clustering jerárquico y matrices de distancia

## ⚙️ Cómo reproducir el proyecto

```bash
# 1. Clonar el repositorio
git clone https://github.com/<usuario>/<repositorio>.git
cd <repositorio>

# 2. Crear un entorno virtual (opcional pero recomendado)
python -m venv venv
source venv/bin/activate      # En Windows: venv\Scripts\activate

# 3. Instalar dependencias
pip install -r requirements.txt

# 4. Ejecutar el notebook
jupyter notebook analysis/auto.ipynb
```

## 📑 Documentación

- 💻 **Código fuente:** [`car_analysis.ipynb`](./analysis/car_analysis.ipynb)
- 🔬 **Análisis completo:** [`car_analysis.html`](./reports/car_analysis.html)
- 📄 **Informe del trabajo:** [`car_analysis_report.pdf`](./reports/car_analysis_report.pdf)
- 🎞️ **Presentación:** [`car_analysis_presentation.pdf`](./presentation/car_analysis_presentation.pdf)

## 📁 Estructura del repositorio

```text
car-analysis/
├── analysis/
│   └── car_analysis.ipynb
├── assets/
│   └── car-hero.png
├── data/
│   └── cars.csv
├── presentation/
│   └── car_analysis_presentation.pdf
├── reports/
│   ├── car_analysis_report.pdf
│   └── car_analysis.html
├── .gitignore
├── index.html
├── README.md
└── requirements.txt
```

## 👤 Autores

- **Fasolato, Matías** — [@MatiFasolato](https://github.com/MatiFasolato)
- **Cortesi, Exequiel** — [@Execortesi](https://github.com/Execortesi)
- **Torregiani, Bautista** — [@BautistaTorregiani](https://github.com/BautistaTorregiani)