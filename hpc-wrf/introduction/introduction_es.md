# Introducción

¿Alguna vez se encontró en una situación en la que usted tiene un paraguas y ninguna lluvia o tarde por la noche en un estacionamiento sin cepillo de nieve porque se le dio lo que usted considera ser desinformación? ¿Estás cansado de que tu meteorólogo local prediga el clima incorrectamente y quieres hacer algo al respecto? Bueno, este es un laboratorio en el que puede encontrar valor. Hoy vamos a hablar y guiarle sobre cómo configurar y ejecutar WRF en la infraestructura de OCI. También proporcionaré un script cuando quieras ejecutar WRF sin saber cómo configurarlo.

Tiempo de laboratorio estimado: 90 minutos

### Acerca de WRF

WRF es la abreviatura de The Weather Research and Forecasting Model. Es un sistema de predicción meteorológica numérica a mesoescala diseñado tanto para aplicaciones de investigación atmosférica como de previsión operativa. El WRF existe hoy en día en una asociación que comenzó a finales de los años 90 entre el Centro Nacional de Investigación Atmosférica (NCAR), la Administración Nacional Oceánica y Atmosférica (representada por los Centros Nacionales para la Predicción Ambiental (NCEP) y el Laboratorio de Investigación del Sistema Terrestre), la Fuerza Aérea de los Estados Unidos, el Laboratorio de Investigación Naval, la Universidad de Oklahoma y la Administración Federal de Aviación (FAA). WRF es de código abierto y está disponible en github. WRF permite a los investigadores simular el clima utilizando datos reales que se han recogido a través de observaciones y análisis o condiciones atmosféricas idealizadas. WRF proporciona una plataforma de previsión operativa, donde muchos contribuyentes han ayudado a realizar avances en física, numérica y asimilación de datos. WRF está actualmente en uso operativo en NCEP y otros centros de previsión internacionalmente, lo que es bastante grande.

### Objetivos

Temas tratados en esta práctica:

*   Descarga de dependencias y WRF
*   Compilación de bibliotecas para WRF/WPS
*   Compilando WRF y WPS
*   Creando dominio de WRF con cuadrícula
*   Descarga de datos de GFS y ejecución de Ungrib y Metgrid
*   Ejecución de WRF con datos reales

### Requisitos previos

En esta práctica se asume que tiene:

*   Una cuenta gratuita de Oracle o de pago en la nube

## Acuses de recibo

*   **Autor**: Brian Bennett, ingeniero de soluciones, recursos informáticos de gran tamaño
*   **Fecha/fecha de última actualización**: Brian Bennett, Big Compute, diciembre de 2020