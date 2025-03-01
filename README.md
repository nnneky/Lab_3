# Laboratorio 3 || FIESTA DE COCTEL

Laboratorio número 3 de Procesamiento Digital de Señales 
- Isabel Sofía Maldonado Roa
- Daniel Guillermo Espinosa Parra.

## Introducción:

Este informe analiza el problema del cóctel, donde múltiples voces se combinan en un entorno con un alto nivel de ruido. Durante el laboratorio, se capturaron señales de audio mediante micrófonos y se aplicaron técnicas de procesamiento digital, como la transformada de Fourier y el análisis de componentes independientes (ICA), con el fin de aislar una voz específica. El objetivo es evaluar la eficacia de dichos métodos en la extracción de señales acústicas dentro de escenarios complejos.


## Requiermimentos: 

-  Interfaz de python (para este caso 3.12)
- numpy 
- scipy.io.wavfile 
- matplotlib.pyplot 
- scipy.signal 
- sklearn.decomposition 
- sounddevice

## Importar las señales 

Para este laboratorio nos pidieron realizar un montaje con 2 micrófonos y dos personas para que a partir de usar ICA se pudieran separar los audios de manera exitosa y escucharlos así por separado, En nuestro caso el micrófono 1 estaba ubicado a 1.23 metros de Isabel y 2.43 metros de Daniel, donde el segundo micrófono estaba ubicado a 1.32 metros de Isabel y a 1.46 metros de Daniel recreando el montaje de la siguiente manera: 

![Captura de pantalla 2025-02-28 094614](https://github.com/user-attachments/assets/36406f97-a59a-46fb-882b-feaf321db9c6)

A continuación se muestra como 
