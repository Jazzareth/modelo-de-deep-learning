# Modelo de deep learning
## Acerca del Dataset 
Se utilizo un modelo para NLP utilizando el dataset de Kaggle "Amazon reviews" . El conjunto de datos de reseñas de Amazon consta de reseñas de productos y usuarios, calificaciones y una reseña en texto sin formato.
Tenemos las sigueintes columnas:
* Polaridad: 1 para negativo y 2 para positivo.
* título: título de la reseña
* texto: reseña
Con  34,686,770 reseñas,el objetivo del modelo es clasificar las reseñas en positivas y negativos. 

## Repositorio
En el repositorio se encuentran los siguientes documentos:
> * Data_cleaning.ipynb: En este documento se encuentra el codigo donde se realizo la concatenación del titulo junto con el texto para tener tanto las palabras clave como la reseña completa al momento de entrenar los datos, se eliminaron los caracteres especiales del texto
> * Modelado.ipynb: En este codigo se encuentra el entrenamiento del embeding para tener un diccionario asi como el entrenamiento del modelo y exportación del mismo
> * Predicciones.ipynb: En este codigo se carga el modelo para hacer predicciones con el dataset de test y texto escrito manualmente
> *  Link de carpeta con los dataset:https://drive.google.com/drive/folders/1myxCRWXAVjtEKckVZTe0jtHW7R041pg_?usp=sharing
> *  Link de la carpeta con el documento json del diccionario: https://drive.google.com/drive/folders/1OBebw3wpRrxBkcxh87xPZPXwYh5FZx1g?usp=sharing

## Codigo 
El código contiene un red neuronal simple de tres capas:
*embedding
*global_average_pooling1d
*Una capa densa con un parametro de salida con la funcion sigmoide. 
Ya que solo tenemos dos clases para este modelo, utilizando la biblioteca de tensor flow, el modelo se entrena con 15 epocas y 50 steps_per_epoch.
Para este tipo de problemas de lenguaje natural es necesario crear un diccionario para tokenizar los textos de entradas para esto se utilizó la biblioteca nltk.

Para el modelo final se realizó una preparación de datos previa al entrenamiento del modelo, en esta limpieza se cambiaron los labels a 0 (negativos) y 1 (positivos), se eliminaron caracteres especiales y se concateno el título junto con el texto completo de la reseña. Para después dividir los 3,000,000 de registros del dataset de train en train y validation con un 70% y 30% de los datos respectivamente, se genera un diccionario con los datos de train para tokenizar el texto para posteriormente entrenar el modelo y hacer predicciones con textos del dataset de test, así como poder introducir el texto manual.  

## Análisis y Reporte sobre el desempeño del modelo.
### Primera iteración
Para la primera iteración la limpieza de datos realizada consistió en cambiar los labels de las reseñas a 0 y 1 y concatenar el texto con el título de la reseña para tener un solo párrafo a analizar ya que el título incluye las palabras claves para la reseña. Una vez realizados estos cambios se creó un diccionario con 10000 palabras las cuales les asignaría un valor numérico.
Una vez creado el diccionario se aplicó la tokenización tanto al dataset de train como el de validación para convertir los textos en valores de un arreglo de números con cada uno de los tokens para cada palabra. 
Este diccionario se importa en un archivo llamado word index.json para utilizarlo en las predicciones. 
Una vez creado el diccionario establecemos un número máximo de tokens para cada párrafo en este caso colocamos 200 para que el modelo se entrene solo con 200 tokens. Una vez terminado el procesado de las entradas pasamos a entrenar el modelo.

Para el accuracy de validación en la primera iteración obtenemos un 74.15% 
Tomando un pequeño segmento del dataset de test para hacer pruebas, tokenizamos los primeros 100 datos para probarlos con el modelo, teniendo en cuenta que tenemos la siguiente cantidad para cada una de las etiquetas:
1   56
0   44

Obtenemos un accuracy de : 19.00%
Con la siguiente matriz de confusión 
|               | Predict 0     | Predict 1     |
| ------------- | ------------- |-------------  | 
| Real 0        | 6             | 50            | 
| Real 1        | 25            | 19            |

Como podemos observar el modelo no está teniendo un buen desempeño ya que si bien pareciera que se inclina más a los 1 observamos que solo tiene 19 unos positivos por lo que el modelo tiene un mal desempeño en general sin importar la cantidad de datos de una clase para entrenarlos.
Esto se puede deber a la complejidad del problema ya que para este modelo estamos entrenando el embedding a la par que el modelo por lo que entre más complejos sean los texto más trabajo le va a costar a nuestro modelo clasificarlo 

introduciendo de manera manual el texto: "really good product, i do not like the color but it work fine", "It was the best day of my life, and then I found this magical product that does incredible stuff", "it was that bad, like very very very bad" obtenemos que todos los clasifica como positivos 

### Segunda iteración
Para poder mejorar el desempeño del modelo se llegó a la conclusión de que se debe trabajar con un texto más limpio por lo que en primer lugar debemos eliminar caracteres especiales y signos de puntuación con la función limpiar texto del documento de data cleaning, ahora la parte más importante del manejo de los datos en esta segunda iteración es correr la función stopwords la cual se encarga de eliminar las palabras sin significado como artículos, pronombres, preposiciones, etc. Estas funciones las aplicamos nuevamente al dataset de train y test para crear un nuevo texto , a partir del cual utilizaremos los mismos parámetros de la iteración uno para crear un nuevo diccionario de 10000 palabras y tokenizar los datos de entrada para el entrenamiento del modelo el cual continúa teniendo la misma estructura para después hacer la validación y predicciones.
Para el accuracy de validación en la segunda iteración obtenemos un 82.54% 
Probamos las predicciones con el mismo dataset de test de 100 datos y obtenemos los siguientes resultados:

Accuracy de : 78.00%
Matriz de confusión 
|               | Predict 0     | Predict 1     |
| ------------- | ------------- |-------------  | 
| Real 0        | 31            | 13            | 
| Real 1        | 9             | 47            |


Como podemos observar una vez realizado el ajuste en el procesamiento del texto para la obtención del diccionario el modelo tuvo un mejor desempeño ya que ahora puede clasificar la mayoría de los textos de manera correcta aumentando en su accuracy en un 8% aproximadamente.Y en el ejemplo manual de texto nos da las siguientes predicciones 1,1,0.
Como conclusión podemos ver que para el modelo donde se entrena el embedding al mismo tiempo que el modelo, tienen un mejor desempeño cuando procesamos el texto eliminando las palabras irrelevantes y repetitivas.

