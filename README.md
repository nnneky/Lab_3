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
min_len = min(len(mic1), len(mic2), len(ruido_blanco)) ### Se calcula la longitud mínima entre las tres señales, len() devuelven el número de muestras de cada señal de audio, min() toma el valor más pequeño de esas tres longitudes y lo almacena en min_len.
mic1, mic2, ruido_blanco = mic1[:min_len], mic2[:min_len], ruido_blanco[:min_len] ## recorta todas las señales de audio a la misma longitud mínima para que tengan la misma cantidad de muestras.

# Ajustar la intensidad del ruido blanco sin saturar la señal
factor_ruido = 0.3 * (np.max(np.abs(mic1)) / np.max(np.abs(ruido_blanco))) ## cálcula la relación de maximos de cada señal y lo reduce aproximadamente un 30% para que el ruido blanco no sea más fuerte que el de los microfonos
ruido_blanco *= factor_ruido ## se le aplica el factor de ruido al ruido blanco

# Restar el ruido a las señales de micrófono
mic1_ruidoso = mic1 - ruido_blanco ## Elimina el ruido blanco de cada señal restándolo de las grabaciones de los micrófonos.
mic2_ruidoso = mic2 - ruido_blanco
```
## FFT Y ESPECTROGRAMAS:

Para la realización del espectograma de cada señal de audio, se debe calcualr la FFT para obtener información de la señal en el dominio de la frecuencia, este proceso se muestra en las siguientes lineas de código: 

```bash
## función para cálcular la FFT de la señal y gráficarla
def calcular_fft(signal, fs, title="Espectro de Frecuencia"): ## se definen los datos que necesita la función  (señal de audio, frecuencia de muestreo)
    """Calcula y grafica la FFT de la señal."""
    freqs = np.fft.fftfreq(len(signal), 1/fs)  # Calcula las frecuencias mediante una función de numpy
    fft_values = np.fft.fft(signal)  # Calcula la FFT de la señal
    plt.figure()
    plt.plot(freqs[:len(freqs)//2], np.abs(fft_values[:len(freqs)//2]))  # Grafica la magnitud usando solo la mitad de las frecuencias (parte positiva)
    plt.title(title)
    plt.xlabel("Frecuencia (Hz)")
    plt.ylabel("Magnitud")
    plt.show()

## Se llaman la función para cálcular la FFT de cada señal con la frecuencia de muestreo de 44 kHz
calcular_fft(mic1, fs1, title="FFT de Microfono 1 Original")
calcular_fft(mic1_ruidoso, fs1, title="FFT de Microfono 1 con Ruido")
calcular_fft(fuente_final, fs1, title="FFT de Fuente Filtrada")
```
Despues de ejecutar estas lineas de código de obtuvieron los siguientes gráficos de la FFT:


![image](https://github.com/user-attachments/assets/23e8d217-155b-47dc-a650-34ad3069c257)


![image](https://github.com/user-attachments/assets/febdeae6-25e9-4947-a115-5d15a33f8741)















