# Random Forest Optimization

Comparación controlada de un Random Forest base, una versión con selección de características y una configuración ajustada mediante validación cruzada.

## Objetivo

Evaluar si reducir variables y ajustar hiperparámetros mejora la clasificación, considerando generalización, interpretabilidad y costo computacional.

## Dataset

**Breast Cancer Wisconsin (Diagnostic)**: 569 observaciones y 30 variables numéricas sobre características de núcleos celulares. Cada observación corresponde a una muestra de una masa mamaria.

- Fuente: [UCI Machine Learning Repository](https://archive.ics.uci.edu/dataset/17/breast+cancer+wisconsin+diagnostic).
- Carga: `sklearn.datasets.load_breast_cancer`; no requiere un CSV externo.
- Objetivo: `maligno`, con 1 = maligno y 0 = benigno.
- Métrica principal: F1 de la clase maligna. Complementarias: recall, precision y ROC-AUC.

## Metodología

1. Inspección de tipos, faltantes, duplicados y distribución del objetivo.
2. División 80/20 estratificada con semilla 42.
3. Random Forest base de 100 árboles con 30 variables.
4. Selección de 15 variables mediante `SelectKBest(f_classif)`.
5. `RandomizedSearchCV`: 12 configuraciones de un espacio de 144, con validación estratificada de 5 folds.
6. Evaluación final de las tres alternativas sobre la misma prueba.

La imputación y selección se ajustan dentro del pipeline en cada fold para evitar fuga de información. La prueba no se utiliza para elegir hiperparámetros. No se estandarizan las variables porque el bosque no requiere escalamiento.

## Resultados

Resultados guardados en el notebook, con Scikit-learn 1.6.1:

| Modelo | Variables | F1 maligno | Recall | Precision | ROC-AUC | Entrenamiento (s) |
|---|---:|---:|---:|---:|---:|---:|
| Base | 30 | 0.9630 | 0.9286 | 1.0000 | 0.9929 | 0.2915 |
| Reducido | 15 | 0.9367 | 0.8810 | 1.0000 | 0.9921 | 0.2365 |
| Optimizado | 15 | 0.9367 | 0.8810 | 1.0000 | 0.9904 | 0.2444 |

La búsqueda consumió **67.33 segundos adicionales**, incluyendo el reajuste automático. Los tiempos dependen del equipo y se midieron una sola vez.

La mejor configuración de la búsqueda fue `n_estimators=100`, `max_depth=8`, `min_samples_split=10` y `max_features="sqrt"`. El término “optimizado” identifica el resultado de la búsqueda; no implica que supere a las alternativas.

**Conclusión:** reducir las variables disminuyó F1 en 0.0263; el ajuste posterior no recuperó esa pérdida. Se recomienda el modelo base, también favorecido por la comparación de validación cruzada. Optimizar exige comprobar la ventaja, sin darla por garantizada.

## Ejecutar en Google Colab

[![Abrir en Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/ierisaac2005-png/Optimizacion_Random_Forest/blob/main/Optimizacion_Random_Forest.ipynb)

Este botón abre Colab. El enlace directo al notebook se completará cuando se confirme la URL del repositorio.

1. Descarga `Optimizacion_Random_Forest.ipynb`.
2. En Colab, selecciona **Archivo → Subir notebook** y carga el archivo.
3. Selecciona **Entorno de ejecución → Ejecutar todas**. No se necesita GPU.

Para conservar cambios en GitHub desde Colab, utiliza **Archivo → Guardar una copia en GitHub**, selecciona el repositorio y revisa la ruta del archivo antes de guardar. El enlace de Colab no sincroniza automáticamente las modificaciones.

## Ejecutar localmente

```bash
python -m pip install -r requirements.txt
jupyter notebook Optimizacion_Random_Forest.ipynb
```

`requirements.txt` fija Scikit-learn en la versión de los resultados guardados; las otras dependencias no están fijadas. No representa un entorno completamente congelado.

## Archivos

- `Optimizacion_Random_Forest.ipynb`: código, explicación, resultados y visualizaciones.
- `README.md`: descripción y ejecución del proyecto.
- `requirements.txt`: dependencias para ejecución local.
- `.gitignore`: exclusión de archivos temporales y entornos locales.

## Limitaciones

Dataset pequeño, una sola partición de prueba y búsqueda acotada. Estos resultados no demuestran superioridad estadística ni validación clínica. Como siguiente paso se propone validación cruzada anidada y repetida, seguida de evaluación externa.

## Autor

Isaac Esquivel Ruiz
