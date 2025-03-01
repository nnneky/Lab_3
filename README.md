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

## Cargar señales de los micrófonos y ruido grabado
## Función diseñada para leer los archivos de audio
def cargar_audio(nombre_archivo): ## la función requiere de un archivo .wav
    ##Carga un archivo de audio y devuelve la tasa de muestreo y los datos.
    fs, data = wav.read(nombre_archivo) ## se llama a la función wav.read de la libreria scipy.io.wavfile para leer el audio, la cual devolvera la frecuencia de muestreo y 
    un arreglo de números que representan el sonido
    return fs, data.astype(np.float32) ## Convierte los datos de audio a float32 (números decimales de precisión simple -> valores entre -1 y 1  que facilitan el 
    procesamiento y evita errores matemáticos)

## Carga los archivos de audio grabados con dos micrófonos y un ruido de fondo a la función cargar_audio.
fs1, mic1 = cargar_audio("mic_1.wav")
fs2, mic2 = cargar_audio("mic_2.wav")
fs_ruido, ruido_blanco = cargar_audio("ruido_blanco.wav")

# Verificar que las tasas de muestreo sean iguales
assert fs1 == fs2 == fs_ruido, "Las tasas de muestreo deben ser iguales" ## mediante assert se valida que las frecuencias de cada audio sean iguales, si no genera un error
 
# Asegurar que todas las señales tengan la misma longitud
min_len = min(len(mic1), len(mic2), len(ruido_blanco)) ### Se calcula la longitud mínima entre las tres señales, len() devuelven el número de muestras de cada señal de audio
mic1, mic2, ruido_blanco = mic1[:min_len], mic2[:min_len], ruido_blanco[:min_len]

# Ajustar la intensidad del ruido blanco sin saturar la señal
factor_ruido = 0.3 * (np.max(np.abs(mic1)) / np.max(np.abs(ruido_blanco)))
ruido_blanco *= factor_ruido

# Restar el ruido a las señales de micrófono
mic1_ruidoso = mic1 - ruido_blanco
mic2_ruidoso = mic2 - ruido_blanco


```




