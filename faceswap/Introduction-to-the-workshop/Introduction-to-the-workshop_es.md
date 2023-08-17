# Introducción al taller

## Lo que aprenderá en este taller

En este taller aprenderá a ejecutar la aplicación [Faceswap](https://faceswap.dev/) en una máquina virtual de GPU en Oracle Cloud Infrastructure. Cubriremos todos los pasos desde el aprovisionamiento y la preparación de la instancia para utilizar los núcleos de GPU para ejecutar el script Python (Faceswap GAN) para la extracción de las caras, entrenando el GAN y la conversión de la cara intercambiada en un video.

Tiempo estimado del taller: 120 minutos (exc. tiempo de entrenamiento de la GAN)

## Qué es deepfake

Deepfake es una palabra compuesta de "aprendizaje profundo" y "fake" y significa medios generados por IA en los que una persona de una imagen o vídeo existente es reemplazada por otra persona o modificada con otras características externas (como edad, peinado, color de la piel, voz, etc.). El acto de manipular vídeos e imágenes ha existido durante mucho tiempo, pero aprovechar el aprendizaje automático y la IA para producir medios con un alto potencial de engaño es nuevo. Es casi imposible distinguir medios reales y falsos a simple vista.

## ¿Cómo funciona Deepfake?

Los deepfakes se basan en redes neuronales artificiales que reconocen patrones (por ejemplo, caras, voces) en datos (fotos o videos de personas). Una gran cantidad de datos se introduce en redes neuronales que se entrenan para reconocer patrones y falsificarlos. Las redes neuronales son algoritmos que se inspiran en el cerebro humano. Una neurona recibe y transmite señales hacia y desde otras neuronas a través de sus sinapsis por axones y dendritas, con el objetivo de, por ejemplo, contraer un músculo. Cuando una señal se mueve de A a B, puede ser pasada porcapas de neuronas. De manera similar, un algoritmo de red neural recibe datos de entrada, los procesa a través de una capa de operaciones matemáticas (multiplicaciones de matriz) equipadas con diferentes pesos, que se ajustan durante el entrenamiento, y finalmente genera una salida.

Para ejecutar multiplicaciones matriciales con 10 mil millones de parámetros de manera oportuna, se necesita una potencia informática adecuada. Es más rápido ejecutar todas las operaciones al mismo tiempo, una tras otra. Una CPU ejecuta un proceso tras otro con un pequeño número de threads, mientras que una GPU facilita la computación en paralelo con un gran número de threads al mismo tiempo. La capacidad de procesar varios cálculos al mismo tiempo, hace que la GPU sea una mejor opción para entrenar algoritmos de red neuronal. Las redes neuronales que se utilizan para los deepfakes se denominan GAN (Generative Adversial Networks), donde dos modelos de aprendizaje automático compiten entre sí. El primer modelo se denomina generador que lee los datos reales y genera datos falsos. El segundo modelo llamado discriminador es responsable de detectar los datos falsos. El generador genera datos falsos hasta que el discriminador ya no pueda detectarlos. Cuantos más datos de entrenamiento estén disponibles para el generador, más fácil será para él generar un profundo terremoto creíble.

## ¿Qué es Faceswap?

La plataforma más conocida y extendida actualmente para generar deepfakes es [Faceswap](https://faceswap.dev/). Es una plataforma de código abierto basada en Tensorflow, Keras y Python y se puede ejecutar en Windows, macOS y Linux. Se ha formado una comunidad activa, que intercambia información sobre Github y otros foros y comparte guiones y sugerencias de soluciones a preguntas.

Puede ejecutar sus cargas de trabajo profundas mediante el nivel gratuito de Oracle, que consta de la oferta Siempre gratis y una prueba gratuita de 30 días. En la capa Always Free, VM.Standard.E2.1. Se puede utilizar Micro y, en la prueba de 30 días, se pueden probar todas las instancias de VM y BM disponibles. Si desea probar la oferta de GPU, puede actualizar su cuenta en la nube de Oracle a PAYG.

## **Acuses de recibo**

*   **Creado por/Fecha**: Maria Patelkou, arquitecto de soluciones de HPC, programa de propuesta a producción de Oracle, marzo de 2021
*   **Fecha/fecha de última actualización**: Maria Patelkou, arquitecto de soluciones de HPC, programa de propuesta a producción de Oracle, marzo de 2021