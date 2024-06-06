# Proyecto de Reconocimiento de Wally/Waldo en Imágenes
 ### Introducción
En este proyecto, desarrollé un sistema de reconocimiento para encontrar al famoso personaje de dibujos animados Wally (o Waldo, dependiendo de la región) en imágenes encontradas en Internet. Para lograr esto, utilicé la técnica de Cascade Trial GUI junto con un conjunto de datos que incluye tanto imágenes positivas (que contienen a Wally) como imágenes negativas (que no contienen a Wally).

## ¿Qué es Cascade Trial GUI y cómo funciona?
Cascade Trial GUI es una herramienta que facilita la creación y el ajuste de clasificadores de detección de objetos utilizando la técnica de cascada de Haar. Esta técnica se basa en características visuales simples que se utilizan para entrenar un clasificador capaz de distinguir entre objetos de interés y fondos.

#### El proceso general de Cascade Trial GUI implica los siguientes pasos:

El proceso general de Cascade Trial GUI implica los siguientes pasos:
1. **Preparación de datos:** Se requiere un conjunto de datos que incluya imágenes positivas (que contienen el objeto de interés, en este caso, Wally) y negativas (que no lo contienen).
2. **Entrenamiento del clasificador:** Utilizando las imágenes de entrenamiento, Cascade Trial GUI entrena un clasificador utilizando la técnica de cascada de Haar. Durante este proceso, el clasificador se ajusta para distinguir características específicas del objeto de interés.
3. **Prueba y ajuste:** Una vez entrenado, el clasificador se prueba con un conjunto de datos de prueba para evaluar su rendimiento. Luego, se pueden realizar ajustes adicionales para mejorar la precisión del clasificador.

4. **Variedad de escenarios:** Al incluir una amplia variedad de imágenes positivas y negativas, el clasificador puede aprender a reconocer al objeto de interés en una variedad de contextos y condiciones de iluminación.
Generalización: Un conjunto de datos diverso ayuda a que el clasificador generalice mejor, es decir, a que sea capaz de detectar al objeto de interés en imágenes que no se parecen exactamente a las del conjunto de entrenamiento.
Reducción de falsos positivos: Al incluir imágenes negativas que sean visualmente similares a las positivas pero que no contengan al objeto de interés, se ayuda a reducir la tasa de falsos positivos del clasificador.

# Codigo y su explicación 
```python
import numpy as np
import cv2 as cv
from screeninfo import get_monitors

# Obtener el tamaño de la pantalla
def get_screen_size():
    monitors = get_monitors()
    if monitors:
        monitor = monitors[0]  # Usar el primer monitor
        return monitor.width, monitor.height
    return 800, 600  # Valor predeterminado en caso de fallo

# Redimensionar la imagen manteniendo la relación de aspecto
def resize_image(image, max_width, max_height):
    height, width = image.shape[:2]
    if width > max_width or height > max_height:
        scaling_factor = min(max_width / width, max_height / height)
        new_width = int(width * scaling_factor)
        new_height = int(height * scaling_factor)
        return cv.resize(image, (new_width, new_height))
    return image

# Cargar el clasificador de rostros
rostro = cv.CascadeClassifier('C:\\Users\\usuario\\OneDrive\\Escritorio\\cascadespruebas\\classifier1\\cascade.xml')

# Leer la imagen
frame = cv.imread("original-images/14.png")
# Obtener las dimensiones de la pantalla
screen_width, screen_height = get_screen_size()

# Redimensionar la imagen para que se ajuste a la pantalla
frame_resized = resize_image(frame, screen_width, screen_height)

# Convertir la imagen a escala de grises
gray = cv.cvtColor(frame_resized, cv.COLOR_BGR2GRAY)

# Detectar rostros
rostros = rostro.detectMultiScale(gray, scaleFactor=1.04, minNeighbors=20, minSize=(55,55))

# Dibujar rectángulos alrededor de los rostros detectados
for (x, y, w, h) in rostros:
    frame_resized = cv.rectangle(frame_resized, (x, y), (x + w, y + h), (0, 255, 0), 2)

# Mostrar la imagen
cv.imshow('Wally', frame_resized)
cv.waitKey(0)
cv.destroyAllWindows()
```
### 1. Importaciones
Se importan los módulos necesarios para el proyecto, incluyendo `numpy`, `cv2` (OpenCV) y `get_monitors` de `screeninfo`.

### 2. Función `get_screen_size()`
Esta función utiliza `get_monitors()` para obtener el tamaño de la pantalla. Si no se puede obtener esta información, se devuelve un valor predeterminado de 800x600.

### 3. Función `resize_image(image, max_width, max_height)`
Esta función redimensiona una imagen manteniendo su relación de aspecto. Se calcula un factor de escala basado en las dimensiones máximas proporcionadas y se aplica este factor a la imagen.

### 4. Carga del clasificador de rostros
Se carga el clasificador de rostros utilizando el archivo XML especificado.

### 5. Lectura de la imagen
Se lee la imagen original desde el archivo "14.png".

### 6. Redimensionamiento de la imagen
La imagen se redimensiona para ajustarse al tamaño de la pantalla utilizando la función `resize_image()`.

### 7. Conversión a escala de grises
La imagen redimensionada se convierte a escala de grises para simplificar el procesamiento.

### 8. Detección de rostros
Se utiliza el método `detectMultiScale()` del clasificador de rostros para detectar rostros en la imagen en escala de grises.

### 9. Dibujar rectángulos
Se dibujan rectángulos alrededor de los rostros detectados en la imagen redimensionada.

### 10. Mostrar la imagen
Finalmente, se muestra la imagen resultante con los rectángulos dibujados utilizando `cv.imshow()`, y se espera a que el usuario presione una tecla antes de cerrar la ventana con `cv.waitKey()` y `cv.destroyAllWindows()`.

![alt text](image-1.png)
### 11. Importancia de tener un gran número de imágenes
Para lograr una alta precisión en la detección de rostros, es fundamental contar con un gran número de imágenes tanto positivas como negativas en el conjunto de datos de entrenamiento. Esto permite al clasificador aprender una amplia variedad de características y contextos en los que pueden aparecer los rostros. Una mayor diversidad en las imágenes de entrenamiento facilita la generalización del clasificador, lo que significa que será capaz de detectar rostros con mayor precisión incluso en condiciones no vistas durante el entrenamiento.