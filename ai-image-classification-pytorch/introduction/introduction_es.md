# Introducción

La implementación de un entrenamiento de clasificación de imágenes es el "Hello World" de la visión informática. Aunque hay muchas publicaciones de blog sobre el tema, la mayoría de ellos presentan la solución sin explicar adecuadamente la API de biblioteca, los principales subyacentes, o ambos. La guía [Formación de un clasificador](https://pytorch.org/tutorials/beginner/blitz/cifar10_tutorial.html) de PyTorch es un buen lugar para empezar, pero algunos de los detalles del código se explican demasiado poco para un lector nuevo en el campo. Este taller se basa en ese tutorial como base con explicaciones línea por línea del código para facilitar una vía de aprendizaje más clara. En esta guía se mostrará cómo entrenar un modelo mediante CPU y, a continuación, explicará cómo actualizar el código para utilizar recursos de GPU. Tenga en cuenta que las instancias de GPU no están disponibles en el nivel libre.

Tiempo de laboratorio estimado: 60 minutos

### Acerca de Tensors

Para mejorar o empeorar, las representaciones visuales ayudan a contextualizar la terminología, las estructuras de datos y los algoritmos de la informática. Para los tensores, [KDnuggets proporciona una explicación clara](https://www.kdnuggets.com/2018/05/wtf-tensor.html). Cuando busca una definición en línea, puede encontrar cierta confusión entre la definición técnica de un tensor y el uso coloquial. Técnicamente, un vector es un tensor de 1 dimensión y una matriz es un tensor de 2 dimensiones, pero la mayoría de las veces el tensor se refiere a un contenedor de n dimensiones que es más grande que un vector y una matriz.

![Representación visual de tensores de diferente tamaño](images/tensor.png)

El término es importante de entender porque los tensores son fundamentales para crear algoritmos de inteligencia artificial y sustentan discusiones sobre la optimización de los recursos informáticos. Un área común de preocupación cuando se entrena un modelo es la utilización de GPU, ya que la [Turing Tensor Cores](https://www.nvidia.com/en-us/data-center/tensor-cores/) de NVIDIA es capaz de lograr una aceleración computacional significativa, pero puede realizar algunos ajustes. Afortunadamente, PyTorch contiene un cargador de datos que asume la responsabilidad de transmitir los tensores a la GPU.

### Objetivos

Temas tratados en esta práctica:

*   Tensores
*   DataLoaders
*   Redes neuronales convolucionales (CNN)
*   Función de pérdida y optimizador
*   Entrenamiento del modelo
*   Aceleración de GPU;

### Requisitos previos

En esta práctica se asume que tiene:

*   Una cuenta gratuita de Oracle o de pago en la nube

## Acuses de recibo

*   **Autor**: Justin Blau, arquitecto sénior de soluciones, Big Compute
*   **Fecha/fecha de última actualización**: Justin Blau, Big Compute, octubre de 2020