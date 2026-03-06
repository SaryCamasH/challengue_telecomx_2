# challenge_telecomx_2
introducción, preparación de datos, EDA, modelado, hallazgos, implicaciones de negocio y recomendaciones


# Predicción de Cancelación de Clientes (Churn) - Telecom X (Parte 2)

## Introducción
Este proyecto es la segunda parte del Challenge Telecom X, enfocado en la construcción de modelos predictivos para anticipar la cancelación de clientes (churn) en una empresa de telecomunicaciones. El objetivo principal es identificar clientes con alto riesgo de abandono para apoyar decisiones estratégicas de retención, continuando el trabajo de análisis exploratorio de datos realizado en la Parte 1.

## Preparación y Análisis de Datos

El proceso inició con la carga del dataset `datos_tratados.csv`, previamente preparado en la Parte 1. La variable objetivo 'Churn' fue inmediatamente convertida a un formato numérico (0 para 'No', 1 para 'Yes'). Se realizó una verificación básica de la estructura del dataset (`df.shape`, `df.info()`, `df.describe()`) para asegurar la integridad de los datos.

Se confirmó la ausencia de valores faltantes en el dataset (`df.isna().sum()` mostró 0 para todas las columnas), lo que simplificó la etapa de preprocesamiento. La columna 'CustomerID', al ser un identificador único sin valor predictivo, fue eliminada. Las variables fueron clasificadas en numéricas (incluyendo la variable objetivo 'Churn', 'Tenure', 'ChargesDaily', 'ChargesMonthly', 'ChargesTotal'), categóricas ('Gender', 'SeniorCitizen', 'Partner', etc.) y binarias.

El análisis de correlación de Pearson entre las variables numéricas y 'Churn' reveló que 'Tenure' y 'ChargesTotal' tenían las correlaciones negativas más fuertes, mientras que 'ChargesDaily' y 'ChargesMonthly' mostraron correlaciones positivas moderadas. Esto sugiere que la antigüedad y los cargos impactan significativamente el churn. Se identificó un desbalance moderado en la variable objetivo (aproximadamente 26.5% de clientes con churn), y se decidió no aplicar técnicas de balanceo en esta etapa, priorizando métricas como Recall y F1-score en la evaluación del modelo.

## Modelado Predictivo

Para el modelado, las variables categóricas fueron transformadas utilizando One-Hot Encoding (`OneHotEncoder` de `sklearn`), y las variables numéricas fueron estandarizadas con `StandardScaler`. El dataset fue dividido en conjuntos de entrenamiento y prueba (80/20 inicialmente y 70/30 para la evaluación final, ambos con `stratify=y` para mantener la proporción de la variable objetivo).

Se entrenaron dos modelos de clasificación:
*   **Regresión Logística:** Seleccionado como modelo base debido a su interpretabilidad y adecuación para problemas binarios. Se configuró con `max_iter=1000` y `class_weight='balanced'` para manejar el desbalance.
*   **Árbol de Decisión:** Un modelo alternativo para capturar posibles relaciones no lineales, configurado con `max_depth=5`.

## Evaluación y Selección del Modelo

La evaluación se centró en métricas clave para problemas de churn:
*   **Accuracy:** Proporción de predicciones correctas.
*   **Precision:** Confiabilidad de las predicciones de churn (verdaderos positivos / (verdaderos positivos + falsos positivos)).
*   **Recall (Sensibilidad):** Capacidad del modelo para identificar a los clientes que realmente cancelan (verdaderos positivos / (verdaderos positivos + falsos negativos)). Prioritario en este contexto.
*   **F1-score:** Media armónica de precisión y recall.
*   **ROC-AUC:** Medida de la capacidad del modelo para distinguir entre clases.
*   **Matriz de Confusión:** Visualización de aciertos y errores por clase.

La Regresión Logística, aunque con un `Accuracy` similar al Árbol de Decisión, mostró un `ROC-AUC` ligeramente superior (aprox. 0.84 vs 0.82) y un mejor equilibrio entre Precision y Recall para la clase Churn. Dada la necesidad de interpretabilidad en un contexto de negocio y la importancia de minimizar falsos negativos (no detectar un cliente en riesgo), la **Regresión Logística fue seleccionada como el modelo final**.

Las variables más importantes identificadas por el modelo de Regresión Logística (basado en el valor absoluto de sus coeficientes) fueron 'Tenure', 'ChargesTotal', 'Contract_Two year', 'InternetService_Fiber optic', 'InternetService_No', entre otras, confirmando su influencia en el comportamiento de churn.

## Hallazgos Clave

Los principales insights derivados del análisis y el modelo predictivo son:
*   **Antigüedad del Cliente (Tenure):** Los clientes con menor antigüedad tienen una mayor propensión a la cancelación.
*   **Tipo de Contrato:** Los contratos mensuales (Month-to-month) están fuertemente asociados con un mayor riesgo de churn, debido a la flexibilidad que ofrecen a los clientes para abandonar el servicio.
*   **Cargos Mensuales (MonthlyCharges):** Los cargos mensuales elevados, especialmente en combinación con una corta antigüedad o servicios específicos, aumentan la probabilidad de churn.
*   **Servicio de Internet (Fiber optic y No Internet Service):** Los clientes con fibra óptica o sin servicio de internet pueden tener patrones de churn diferenciados, posiblemente relacionados con la calidad o la necesidad del servicio.

## Implicaciones de Negocio

El modelo predictivo de churn tiene implicaciones estratégicas significativas para Telecom X:
*   **Identificación Temprana de Riesgo:** Permite identificar a clientes con alta probabilidad de cancelación antes de que el evento ocurra, brindando una ventana de oportunidad para la intervención.
*   **Segmentación de Clientes:** Facilita la segmentación de la base de clientes según su nivel de riesgo, permitiendo estrategias de retención más focalizadas.
*   **Optimización de Recursos:** Al dirigir los esfuerzos de retención hacia los segmentos de mayor riesgo, se optimiza el uso de recursos y se mejora el retorno de la inversión en campañas.
*   **Toma de Decisiones Basada en Datos:** Proporciona un insumo analítico sólido para la toma de decisiones comerciales y operativas.

## Recomendaciones Estratégicas

Se sugieren las siguientes acciones concretas:
*   **Campañas Focalizadas:** Implementar campañas de retención dirigidas a los segmentos de clientes identificados con mayor riesgo, ofreciendo incentivos personalizados, mejoras de servicio o planes tarifarios ajustados.
*   **Alertas Automáticas:** Desarrollar un sistema de alertas automáticas que notifique a los equipos de negocio cuando un cliente supere un umbral de riesgo de churn definido por el modelo.
*   **Ofertas Personalizadas:** Crear ofertas de retención adaptadas a los factores que más influyen en la decisión de cancelación para cada segmento (e.g., descuentos en cargos mensuales, mejoras de servicio, extensión de contrato).
*   **Monitoreo Continuo:** Establecer un proceso de monitoreo periódico del desempeño del modelo y de los indicadores de churn en producción. 
*   **Incorporación de Nuevas Variables:** Explorar la integración de variables adicionales, como datos de interacción con el servicio al cliente, información demográfica externa, o feedback de encuestas de satisfacción, para mejorar la capacidad predictiva.
*   **Implementación en Sistemas de Negocio:** Integrar el modelo predictivo en los sistemas operativos y herramientas de gestión de clientes existentes para una aplicación fluida y en tiempo real.

Este proyecto sienta las bases para una estrategia de retención proactiva y analíticamente informada para Telecom X, transformando el análisis de datos en acciones de negocio tangibles.

    f.write(readme_content)

print("README.md file generated successfully.")
