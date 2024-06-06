# Reconocimiento Facial con OpenCV

El reconocimiento facial es una tecnología de identificación biométrica que utiliza las características faciales para identificar a individuos. En este documento, explicamos cómo implementar un sistema básico de reconocimiento facial utilizando la biblioteca OpenCV en Python.

## Código de Reconocimiento Facial

A continuación, presentamos un ejemplo de código que captura imágenes de una cámara web, detecta caras y guarda imágenes de las caras detectadas en una carpeta específica.

```python
import cv2 as cv
import numpy as np

face = cv.CascadeClassifier('haarcascade_frontalface_alt.xml')
cap = cv.VideoCapture(0)
i = 0

while True:
    ret, frame = cap.read()
    gray = cv.cvtColor(frame, cv.COLOR_BGR2GRAY)
    faces = face.detectMultiScale(gray, 1.3, 5)

    for (x, y, w, h) in faces:
        frame2 = frame[y-10:y + h +10, x-10:x + w +10]
        frame2 = cv.resize(frame2, (100, 100), interpolation=cv.INTER_AREA)
        cv.imwrite("emociones/feliz/balta" + str(i) + ".png", frame2)
    
    cv.imshow('faces', frame)
    i = i + 1
    k = cv.waitKey(1)
    if k == 27:
        break

cap.release()
cv.destroyAllWindows()