Predicción de Readmisión de Pacientes Diabéticos
En este proyecto, presento mi flujo de trabajo completo como Científico de Datos y Médico para abordar un desafío clínico complejo: la predicción de readmisiones hospitalarias tempranas (<30 días) en pacientes diabéticos.

Utilicé un dataset público de 100,000 pacientes del UCI, que es conocido por sus desafíos de limpieza y desbalanceo de clases.

El Desafío: Datos Sucios y Desbalanceados
Mi primer objetivo fue simular un escenario real de ciencia de datos. El dataset crudo presentaba varios retos significativos:

Datos Sucios: Los valores nulos no estaban codificados como NaN, sino como ?.

Nulos Masivos: Columnas críticas como weight (peso) y medical_specialty (especialidad) tenían más del 40% (y hasta 97%) de datos faltantes.

Datos Desbalanceados: Mi variable objetivo (readmisión <30 días) representaba solo el 11% de los casos (una proporción de 8:1), lo cual es un reto clásico en el diagnóstico médico.

Mi Proceso (El "Súper Trabajo")
Mi análisis se dividió en dos fases principales: una limpieza profunda y una ingeniería de características guiada por mi dominio clínico.

1. Limpieza y Preparación de Datos
Cargué los datos directamente desde el archivo .zip de origen usando requests y zipfile.

Convertí explícitamente todos los valores ? a NaN para poder cuantificarlos.

Tomé una decisión ejecutiva: eliminé 5 columnas (incluyendo weight y payer_code) que eran inutilizables debido a la masiva cantidad de datos faltantes.

Eliminé las pocas filas que tenían nulos en datos demográficos clave (como race).

2. Ingeniería de Características (Mi Aporte Clínico)
Aquí es donde mi perfil híbrido marcó la diferencia:

Decisión Clínica: Analicé los nulos en A1Cresult y max_glu_serum. DeterminTé que estos NaN (más de 174,000) no eran "datos faltantes", sino que representaban "Prueba No Realizada". En lugar de eliminar estos datos, creé una nueva categoría, 'None', tratando la "no-prueba" como una característica predictiva en sí misma.

Codificación: Identifiqué 37 columnas categóricas (incluyendo IDs numéricos como admission_type_id que el modelo podría malinterpretar) y 7 columnas numéricas reales.

Apliqué One-Hot Encoding (pd.get_dummies) a las 37 columnas categóricas, lo que expandió mi dataset de 45 a 2,374 características listas para el modelo.

Finalmente, normalicé las 7 columnas numéricas usando StandardScaler de Scikit-Learn.

Hallazgos del Modelo y la Paradoja de la Exactitud
Para manejar el desbalanceo 8:1, entrené mis modelos usando class_weight='balanced'. Los resultados fueron fascinantes.

Hallazgo 1: El Fracaso del Modelo Complejo
Mi primer modelo, un RandomForestClassifier con las 2,374 características, obtuvo un impresionante 89% de accuracy.

El Problema: Al analizar la matriz de confusión, descubrí que este "accuracy" era una trampa. El modelo lograba esta puntuación simplemente prediciendo 'No Readmitido' para casi todos los pacientes.

Métrica Clínica (Recall): 0.00%

Conclusión: Este modelo era un fracaso clínico total.

Hallazgo 2: La Superioridad del Modelo Simple
Mi segundo modelo, una Regresión Logística, obtuvo un accuracy mucho más bajo (66%).

El Hallazgo Clave: A pesar de su baja precisión general, este modelo logró un Recall (Sensibilidad) del 56%.

Conclusión: En un escenario hospitalario real, un modelo que "captura" al 56% de los pacientes de alto riesgo (para darles seguimiento) es infinitamente más valioso que un modelo con 89% de accuracy que no captura a nadie.

Este proyecto demuestra que en la ciencia de datos aplicada a la salud, el accuracy es a menudo una métrica engañosa, y el Recall (Sensibilidad) es el verdadero indicador de valor.

Artefactos del Proyecto
Este repositorio incluye:

analisis_readmisiones.ipynb: El notebook de Jupyter con todo el proceso.

modelo_readmision_logistica.joblib: El modelo de Regresión Logística entrenado (el "ganador").

scaler_readmision.joblib: El objeto StandardScaler entrenado.

columnas_modelo_readmision.joblib: La plantilla de las 2,374 columnas del modelo.
