# Laboratorio 3 ||  Procesamiento Digital de Señales 
Laboratorio numero 3 de Procesamiento Digital de Señales | Isabel Sofía Maldonado Roa y Daniel Guillermo Espinosa Parra.

## Introducción:

Este informe analiza el problema del cóctel donde multiples voces se mezclan en un entorno con bastante cantidad de ruido. En este laboratorio se capturaron señales de voz con microfonos  y se aplicaron técnicas de procesamiento digital de señales como la transformada de fourier y el ICA, para poder separar una vos ezpecifica, el objetivo es evaluar la efectividad de esteos métodos para la extracción de señales acusticas en entornos complejos. 


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
