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

En este laboratorio, se llevó a cabo un montaje utilizando dos micrófonos y dos personas con el objetivo de aplicar el método ICA para separar y escuchar las señales de audio de manera individual. En la configuración empleada, el primer micrófono se ubicó a 1.23 metros de Isabel y a 2.43 metros de Daniel, mientras que el segundo se posicionó a 1.32 metros de Isabel y a 1.46 metros de Daniel, recreando así el montaje diseñado para el experimento.

![Captura de pantalla 2025-02-28 094614](https://github.com/user-attachments/assets/36406f97-a59a-46fb-882b-feaf321db9c6)

Para la grabación de los audios, se decidió utilizar el formato .wav, debido a la facilidad de manipulación de la extensión mediante la librería  scipy.io.wavfile, además se eligió una frecuencia de muestreo de audio común (44 kHz) ya que esta nos proporciona equilibrio entre calidad y eficiencia en la reproducción del sonido, evitando problemas como el aliasing (distorsión que ocurre cuando una señal se muestrea a una frecuencia insuficiente, causando que las altas frecuencias se confundan con frecuencias más bajas). Como primer paso, se procedio a encender las grabaciones en cada micrófono al mismo tiempo, para esta oportunidad se grabaron 20 segundos de ruido blanco.Seguido a esto se procedio a grabar las voces cojuntas, cada persona relato una fabula diferente del escritor Rafael Pombo, pronunciandolas al tiempo, haciendo que ambas voces se encuentren en la grabación, esto durante aproximadamente 20 segundos.

A continuación se muestra como se realizó la manipulación de los audios de cada micrófono, según los items requéridos.


```bash
## librerias utilizadas para el desarrollo del código 
import numpy as np ## Permite trabajar con arreglos numéricos y cálculos matemáticos.
import scipy.io.wavfile as wav ## leer y escribir archivos de audio en formato .wav
import matplotlib.pyplot as plt ## Para graficar la FFT y espectrogramas.
from scipy.signal import butter, filtfilt ## Para aplicar filtros a las señales de audio.
from sklearn.decomposition import FastICA ## Para realizar la separación de fuentes con Análisis de Componentes Independientes (ICA).
import sounddevice as sd ## Para reproducir el audio procesado.


```



