# Pipeline Mejorado MLOps para diagnóstico médico de enfermedades comunes

## Objetivo del proyecto

Desarrollar un sistema basico de ML capaz de predecir si un paciente puede padecer una enfermedad o no, y su gravedad, a partir de algunos de sus síntomas, con un pipeline de MLOps reproducible. El sistema debe poder ser usado por médicos desde su computador local (en casos ligeros) o a través de un servicio en la nube.

## Supuestos clave

1. **Tipos de datos**: Datos estructurados con variables clínicas (fiebre, dolor y frecuencia cardiaca). No se incluyen imágenes.
2. **Volumen de datos**: El sistema debería poder esperar cuál volumen de datos, y poder escalar horizontal o verticalmente dependiendo de las necesidades
3. **Privacidad**: Los datasets médicos normalmente pasan por un proceso de anonimización antes de ser compartidas al público. Si por alguna razón no es el caso, los datos se anonimizarán según algún estándar médico como (HIPAA).
4. **Usuarios finales**: Médicos profesionales con conocimientos mínimos en tecnología.
5. **Despliegue**: Este pipeline está planteado como un ejercicio académico de un curso de una maestría de inteligencia artificial aplicada. No obstante, se espera que las tecnologías y el despliegue tengan las características propias de un proyecto profesional y eso incluye el despliegue en la nube de AWS
6. **Calidad del diagnóstico**: Dada la sensibilidad del contexto médico, se espera que los modelos den unas tazas de precisión lo más elevadas posibles (apuntar a un mínimo 85% de precisión y/o buen performance en las diferentes métricas). De todas maneras, esta herramienta no reemplaza el diagnostico medico de un profesional y servirá solo como apoyo
7. **Tecnologías base**: Se usará python como lenguaje de programación principal debido a la gran variedad de librerias que tiene para ML. De todas las nubes existentes se decide usar AWS debido a su gran soporte, estabilidad y soporte

## Proceso End-to-End

### 1. **Ingesta y Preprocesamiento de Datos**

- Datos extraídos de fuentes públicas (como [Orphanet](https://www.orpha.net/) o [datos.gov.co](www.datos.gov.co/Salud-y-Protecci-n-Social/Diagnostico-por-consultas-en-trimestres/ncap-5idg)).
- Ingesta automatizada con scripts Python
- Limpieza (valores faltantes, duplicados, etc).
- Codificación de variables categóricas (Dummy encoding)
- Estandarización numérica

### 2. **Almacenamiento**

- Diseño de esquema relacional para pacientes, síntomas y diagnósticos.
- Inserción de datos preprocesados en las maquinas locales y en el servicio de nube de AWS
- Protección de datos sensibles mediante roles y conexión cifrada (SSL).
- Implementación de tecnicas de replicación de datos

### 3. **Selección de Características**

- Exploración visual (seaborn/matplotlib).
- Análisis de correlación.
- Eliminación de características redundantes.
- Reducción dimensional (PCA, si es necesario).

### 3. **Exploración de datos**

- Exploración visual
- Análisis de correlación
- Eliminación de características redundantes o con alta correlación
- Reducción dimensional (como PCA, si se considera necesario)

### 4. **Entrenamiento**

- Partición del conjunto de entrenamiento y conjunto de prueba
- Probar diferentes modelos de ML
- Ajuste de Hiperparámetros inicial

### 5. **Validación**

- Evaluación con AUC-ROC, Precision-Recall, F1, etc
- Validación cruzada.
- Ajuste de continuo de Hiperparámetros y vuelta al entrenamiento en caso de ser necesario

### 6. **Empaquetado**

- Se propone el uso de Docker para empaquetar el codigo, dependencias y configuración del entorno donde se pretenda desplegar el modelo
- Se define el Docker file pertinente para cada uno de los dos posibles despliegues

### 7. **Despliegue**

- **FastAPI** para ejecución local (modo ligero).
- **AWS Lambda** para uso remoto por médicos.
- API REST para predicciones, documentada con Swagger.

- Se definen 2 opciones: **modo local** y **modo servicio web en nube**.

### 8. **Monitoreo**

- Latencia, tasa de error, distribución de inputs/output.
- Dashboards con datos relevantes
- Configiración de alertas de errores de relevancia

### 9. **Reentrenamiento**

- detección cambios y disparo de reentrenamiento en caso de ser necesario
- Modelos reentrenados pasan por la misma validación antes de reemplazar el actual.

---

## Tecnologías

| Etapa                        | Tecnología                       | Justificación comparativa                                                                                                                                                                                            |
| ---------------------------- | -------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
|                              |
| Ingesta y Preprocesamiento   | Pandas                           | Pandas es ideal para datos tabulares en etapas iniciales, como la ingesta y el preprocesamiento. Se podría complementar con Numpy o librerías similares                                                              |
| Almacenamiento               | PostgreSQL                       | Nuestro modelo tabular se ajusta el modelo relacional, PostgreSQL es una base de datos relacional popular y que tiene complementos y soporte en las principales nubes                                                |
| Exploración de datos         | Seaborn                          | Permite visualizar dinamicamente los datos para encontrar hallazgos de relevancia, a diferencia de Mathplotlib donde sus graficos son mas estaticos y planos                                                         |
| Entrenamiento                | scikit-learn                     | Scikit-learn es muy versátil y se usa habitualmente para la construcción y entrenamiento de los modelos                                                                                                              |
| Validación                   | MLflow                           | En comparación con otras tecnologías como Weights o Neptune, MLflow es open source y facilita despliegues locales dado el caso, lo que es ideal dado los requerimientos                                              |
| Empaquetado                  | Docker                           | Docker es el estándar de la industria para contenerización, y es la tecnología con la que el equipo de desarrollo tiene más experiencia                                                                              |
| Despliegue                   | FastAPI (local) / AWS Lambda     | FastAPI es una librería de Python apta para trabajar local, dado que es más liviano que alternativas de Python como Django Rest Framework. AWS Lambda será nuestra alternativa para hacer la API en la nube de AWS   |
| Monitoreo                    | Prometheus                       | Esta tecnología proporciona métricas en tiempo real que se adecuan para el monitoreo y alertas tempranas de para el servicio propuesto                                                                               |
| Reentrenamiento              | Apache Airflow + MLflow          | Airflow automatiza triggers y flujos recurrentes, mientras que MLflow gestiona modelos. Son alternativas más especificas para ML que tecnologias mas generales como Jenkins, por ejemplo                             |

---

## Diagrama General del Pipeline

![Postman](pipeline.svg)
