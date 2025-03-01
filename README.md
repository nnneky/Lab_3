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

# --- Combinación y Reducción de Ruido ---
mic_combinado = (mic1 + mic2) / 2.0 ## Se promedian las señales de Micrófono 1 y Micrófono 2, combinando ambas en una sola, evitando diferencias individuales
factor_ruido = 0.3 * (np.max(np.abs(mic_combinado)) / np.max(np.abs(ruido_blanco))) ## se reduce la amplitud del ruido un 30%
ruido_blanco *= factor_ruido ## se palica lo anterior al ruido blanco
mic_combinado_ruidoso = mic_combinado - ruido_blanco ## se purga la señal combinada del ruido blanco

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
calcular_fft(mic1, fs1, title="FFT de Microfono 1")
calcular_fft(mic2, fs1, title="FFT de Microfono 2 ")
```
Despues de ejecutar estas lineas de código de obtuvieron los siguientes gráficos de la FFT:


![image](https://github.com/user-attachments/assets/23e8d217-155b-47dc-a650-34ad3069c257)


![image](https://github.com/user-attachments/assets/febdeae6-25e9-4947-a115-5d15a33f8741)


![image](https://github.com/user-attachments/assets/2de7af55-a3c3-4e5b-8959-bf4cefa60be3)


Mediante estas imagenes podemos dectar en que frecuencias se presenta más energía, para este caso se estima que el rango podria ser entre 500 y 3000 Hz

Luego de cálcular la FFT, se procede a realizar el espectograma:

```bash
def mostrar_espectrograma(signal, fs, title="Espectrograma"): ## variables necesarias para el proceso
    plt.figure(figsize=(10, 4))
    plt.specgram(signal, Fs=fs, NFFT=1024, noverlap=512, cmap="inferno")  # Genera el espectrograma (transforma la señal de audio en una representación tiempo-frecuencia) dividiendo la señal en ventanas ( con 1024 muestras cada una) y solapandola para evitar cortes abruptos
    plt.title(title)
    plt.xlabel("Tiempo (s)")
    plt.ylabel("Frecuencia (Hz)")
    plt.colorbar(label="Intensidad (dB)")
    plt.show()

mostrar_espectrograma(mic1, fs1, title="Espectrograma de Microfono 1")
mostrar_espectrograma(mic2, fs1, title="Espectrograma de Microfono 2")

```
De lo anterior se obtuvo lo siguiente:


![image](https://github.com/user-attachments/assets/73ced020-32f4-41b6-904d-8a82fa2a3040)

El espectrograma del Micrófono 1 muestra una mayor presencia de energía en frecuencias bajas y medias (entre 0 y 5 kHz), lo cual es típico de una señal de voz.


![image](https://github.com/user-attachments/assets/cffb56aa-c0c1-4bcf-af56-97873b430aa3)

El espectrograma del Micrófono 2 muestra una distribución de frecuencias con componentes fuertes alrededor de 10 kHz y 12.5 kHz, lo que sugiere presencia de ruido constante o interferencias. La mayor concentración de energía está en las frecuencias bajas y medias (debajo de 5 kHz), lo cual es típico de señales de voz. 

La distribución espectral muestra que el micrófono 1 podría tener una mejor captura de la voz sin tanta contaminación de ruido.

## SEPARACIÓN DE COMPONENTES (ICA) Y FILTRADO:

Para poder aislar solo una voz y poder reproducirla, se deben separar los componentes de la señal combinada y filtrarlos, ya que por medio de los gráficos de la FFT y el espectrográma se observo mayor presencia de energía entre las frecuencias de entre 300 y 5000 Hz se realizará un filtro digital para ese intervalo.

```bash

## función para el filtro digital
def filtro_pasabanda(signal, fs, lowcut, highcut, order=6): ## variables y parametros  para la función
    nyquist = 0.5 * fs ## la frecuencia de Nyquist (mitad de la frecuencia de muestreo)es el límite superior para representar señales sin aliasing.
    low = lowcut / nyquist ## se normaliza la frecuencia baja respecto a la frecuencia de Nyquist
    high = highcut / nyquist ## se normaliza la frecuencia alta respecto a la frecuencia de Nyquist
    b, a = butter(order, [low, high], btype='band') ## se crean las especificaciones del filtro
    return filtfilt(b, a, signal) ## se aplica el filtro dos veces para que no haya desface

### Separación de Fuentes con ICA
ica = FastICA(n_components=1, max_iter=1000, tol=0.0001) ## Se Crea un objeto FastICA para realizar la separación de fuentes
fuente_extraida = ica.fit_transform(mic_combinado_ruidoso.reshape(-1, 1)).flatten() ## Aplica el algoritmo ICA para extraer la fuente independiente más significativa.
fuente_filtrada = filtro_pasabanda(fuente_extraida, fs1, 500, 3000, order=8) ## Aplica un filtro pasa banda para dejar solo las frecuencias con más energía (voz)(500-3000 Hz) y eliminar ruidos 

# Normalización
fuente_filtrada /= np.max(np.abs(fuente_filtrada)) ## Normaliza la señal para que su valor máximo sea 1 o -1, evitando saturación

### Guardado y Reproducción de Señal Filtrada
wav.write("fuente_unica_filtrada.wav", fs1, np.int16(fuente_filtrada * 32767)) ## Guarda la señal filtrada como un archivo de audio WAV.   convirtiendola de float32 a int16
sd.play(fuente_filtrada, fs1) ## reproduce la señal filtrada
sd.wait()

```
Al igual que a las señales de audio anteriores, mediante funciones creadas se determinó el gráfico de FFT y el espectrograma




## Analísis de SNR: 

El snr es la relación entre la potencia de la señal y la potencia del ruido en un sistema. Se expresa en decibelios (dB) y mide la calidad de la señal, un valor alto genera menos interferencia entre el ruido y la señal orignial 







