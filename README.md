# Predicción del Peso Final de Pollos de Engorde

Caso práctico de Ciencia de Datos aplicado a la avicultura. El objetivo es **predecir el
peso final (`Peso_Final_g`)** de un lote de pollos a partir de variables de manejo, ambiente
y genética, y traducir el modelo en **conclusiones de negocio capitalizables y auditables**:
qué línea genética conviene, qué variables gobiernan el engorde, cuánto alimento se consume y
dónde está el dinero.

Todo el trabajo es **reproducible** (semilla fija `random_state = 42`) y está desarrollado
íntegramente en Python.

---

## Estructura del repositorio

```
proyecto_pollos/
├── data/
│   └── Data_caso_practico.xlsx          # Datos de origen
├── notebooks/
│   └── PIPELINE_CASO_PRACTICO.ipynb         # Pipeline completo, ejecutado y verificado
├── informe/
│   ├── informe_latex.txt                # Fuente LaTeX del informe técnico
│   ├── informe_latex.pdf                # Informe compilado (15 págs.)
│   └── figuras/                         # Figuras embebidas en el informe
├── outputs/
│   ├── figures/                         # 10 figuras del análisis (PNG)
│   └── tablas/                          # Tablas y resumen ejecutivo (CSV / JSON)
├── requirements.txt                     # Dependencias con versiones verificadas
└── README.md                            # Este archivo
```

---

## Reproducción en 3 pasos

### 1. Instalar dependencias

```bash
pip install -r requirements.txt
```

### 2. Ejecutar el cuaderno de extremo a extremo

```bash
cd notebooks
jupyter nbconvert --to notebook --execute --inplace pipeline_pollos.ipynb
```

Esto vuelve a generar las 10 figuras en `outputs/figures/`, las tablas en `outputs/tablas/`
y el archivo `resumen_ejecutivo.json` con las métricas finales. También puede abrirse y
ejecutarse de forma interactiva con `jupyter notebook` o en VS Code.

### 3. (Opcional) Compilar el informe en PDF

```bash
cd informe
pdflatex informe_latex.tex      # primera pasada
pdflatex informe_latex.tex      # segunda pasada (índice y referencias cruzadas)
```

> El informe usa el paquete `babel` en español (estándar en cualquier distribución TeX
> completa como TeX Live o MiKTeX, y en Overleaf). Las figuras se buscan en `./figuras/`.

---

## ¿Qué hace el pipeline? (las 7 etapas del encargo)

1. **Conocer el dato** — diccionario de variables, calidad (nulos, duplicados, centinelas),
   estadísticos y detección de inconsistencias.
2. **Preparar el terreno** — tratamiento de faltantes y *outliers* + **ingeniería de
   características documentada** (`Edad_Dias_sq`, `THI`, `Sexo_macho`), incluida solo si aporta.
3. **EDA** — curva de crecimiento, correlación, distribuciones, dispersión por grupos,
   información mutua, PCA y capacidad predictiva incremental por bloque de variables.
4. **Selección de variables** — con **control estricto de fuga de información** (las variables
   derivadas del objetivo —ganancia, GDP, FCR— se excluyen del modelo).
5. **Modelado** — 11 modelos, de un *baseline* trivial a *gradient boosting*.
6. **Comparación de métricas** — validación cruzada (RMSE, MAE, R², MAPE) y elección del
   campeón por **parsimonia**.
7. **Importancia y recomendaciones** — importancia por permutación, efecto ajustado de la
   línea genética (IC 95 %) y **monetización auditable**.

---

## Resultados clave

| Resultado | Valor |
|---|---|
| **Modelo campeón** | Regresión **Lasso** (lineal regularizada) |
| RMSE (test) | **83.08 g** |
| MAE (test) | 64.58 g |
| R² (test) | **0.9921** |
| MAPE (test) | **5.82 %** |
| Efecto del sexo (Macho) | **+126.4 g** (IC 95 %: 113.2 – 139.7) |
| Mejor línea genética | **Cobb 500** → +44.3 g (IC 95 %: 28.1 – 60.5) |
| Segunda línea | Ross 308 → +25.2 g (IC 95 %: 9.1 – 41.4) |
| Impacto económico (Cobb 500) | **≈ 1 481 USD / lote** de 10 000 aves |

### Hallazgos destacados

- **El crecimiento es esencialmente lineal** (1–42 días): la edad sola explica el 98.1 % de
  la varianza. Por eso un **modelo lineal simple supera a los árboles** (XGBoost, LightGBM, RF).
- **El sexo pesa más que la genética**: el manejo diferenciado por sexo es la mayor palanca.
- **Cobb 500 es la línea que más engorda** en este dato, coherente con la literatura para
  clima tropical, y con consumo diario comparable al de las otras líneas.
- **Las variables ambientales (temperatura, humedad, mortalidad) aparecen como ruido**
  (información mutua nula). Es un **problema de calidad del dato**, no una verdad biológica:
  se recomienda mejorar la sensórica y el registro por galpón.

---

## Limitaciones

- El dato presenta rasgos **sintéticos** (relación lineal con intercepto no físico ~260 g, y
  peso a día 1 biológicamente alto). Se documenta como limitación y **no se altera** el dato.
- Sin **consumo acumulado** no se calcula el FCR real; queda como recomendación de registro.
- La monetización usa **precios de mercado externos auditables** (referencias del sector
  avícola ecuatoriano), no precios internos de la empresa; recalibrar con datos propios.

---

## Fuentes

Las referencias bibliográficas (estudios comparativos de líneas genéticas) y las fuentes de
precios del mercado avícola ecuatoriano están detalladas en la última sección del informe
(`informe/informe_latex.pdf`).

---

*Cuaderno reproducible — semilla `42`. Métricas generadas por la ejecución de
`notebooks/PIPELINE_CASO_PRACTICO.ipynb`.*
