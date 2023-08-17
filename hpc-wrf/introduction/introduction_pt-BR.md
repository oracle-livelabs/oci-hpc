# Introdução

Já se encontrou em uma situação em que você tem um guarda-chuva e sem chuva ou tarde da noite em um estacionamento sem neve, porque lhe foi dado o que você considera serem informações falsas? Você está cansado de seu meteorologista local prever o tempo incorretamente e você quer fazer algo sobre isso? Bem, este é um laboratório em que você pode encontrar valor. Hoje vamos discutir e orientá-lo sobre como configurar e executar o WRF na infraestrutura de OCI. Também vou fornecer um script apenas para que você queira executar WRF sem aprender a configurá-lo.

Tempo estimado de laboratório: 90 minutos

### Sobre o WRF

WRF é a abreviação de The Weather Research and Forecasting Model. Trata-se de um sistema de previsão de tempo numérica de mesoscale projetado para aplicações de pesquisa atmosférica e previsão operacional. A WRF existe hoje uma parceria que começou no final dos anos 90 entre o Centro Nacional de Pesquisa Atmosférica (NCAR), a Administração Oceânica e Atmosférica Nacional (representada pelos Centros Nacionais de Previsão Ambiental (NCEP) e o Laboratório de Pesquisa do Sistema Terrestre), a Força Aérea dos EUA, o Laboratório de Pesquisa Naval, a Universidade de Oklahoma e a Administração Federal de Aviação (FAA). O WRF é de código aberto e está disponível no github. A WRF permite que os pesquisadores simulem o clima usando dados reais que foram coletados por meio de observações e análises ou condições atmosféricas idealizadas. A WRF fornece uma plataforma de previsão operacional, onde muitos colaboradores ajudaram a fazer avanços em física, numéricos e assimilação de dados. A WRF está atualmente em uso operacional na NCEP e em outros centros de previsão internacionalmente, o que é muito grande.

### Objetivos

Tópicos abordados neste laboratório:

*   Fazendo download de dependências e WRF
*   Compilando bibliotecas para WRF/WPS
*   Compilando WRF e WPS
*   Criando domínio WRF com Geogrid
*   Fazendo download de dados GFS e executando Ungrib e Metgrid
*   Executando WRF com dados reais

### Pré-requisitos

Este laboratório pressupõe que você tenha:

*   Uma conta no Oracle Free Tier ou Paid Cloud

## Confirmação

*   **Autor** - Brian Bennett, Engenheiro de Soluções, Big Compute
*   **Última Atualização por/Data** - Brian Bennett, Big Compute, dezembro de 2020