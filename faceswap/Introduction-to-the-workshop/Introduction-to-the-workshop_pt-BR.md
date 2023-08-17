# Introdução ao workshop

## O que você aprenderá neste workshop

Neste workshop, você aprenderá a executar o aplicativo deepfake [Faceswap](https://faceswap.dev/) em uma máquina virtual de GPU no Oracle Cloud Infrastructure. Abrangeremos todas as etapas do provisionamento e da preparação da instância para utilizar os núcleos de GPU para executar o script Python (Faceswap GAN) para a extração das faces, treinando a GAN e a conversão da face trocada em um vídeo.

Tempo estimado de oficina: 120 minutos (exceto tempo de treino do GAN)

## O que é deepfake

Deepfake é uma palavra composta de "aprendizagem profunda" e "falsa" e significa mídia gerada por IA em que uma pessoa em uma imagem ou vídeo existente é substituída por outra pessoa ou modificada com outras características externas (como idade, penteado, cor da pele, voz etc.). O ato de manipular vídeos e imagens já existe há muito tempo, mas aproveitar o aprendizado de máquina e a IA para produzir mídia com alto potencial de engano é novo. É quase impossível distinguir a mídia real e falsa com o olho nu.

## Como funciona o deepfake?

As deepfakes são baseadas em redes neurais artificiais que reconhecem padrões (por exemplo, rostos, vozes) em dados (fotos ou vídeos de pessoas). Uma grande quantidade de dados é inserida em redes neurais que são treinadas para reconhecer padrões e falsificá-los. Redes neuronais são algoritmos inspirados no cérebro humano. Um neurônio recebe e transmite sinais de e para outros neurônios através de suas sinapses sobre axônios e dendritos, com o objetivo de, por exemplo, contrair um músculo. Quando um sinal se move de A para B, ele pode ser passado por camadas de neurônios. Da mesma forma, um algoritmo de rede neural recebe dados de entrada, processa-os através de uma camada de operações matemáticas (múltiplas matrizes) equipadas com diferentes pesos, que são ajustados durante o treinamento, e finalmente gera uma saída.

Para executar multiplicações de matriz com 10 bilhões de parâmetros em tempo hábil, é preciso poder computacional adequado. É mais rápido executar todas as operações ao mesmo tempo, em vez de uma após a outra. Uma CPU executa um processo depois do outro com um pequeno número de threads, enquanto uma GPU facilita a computação paralela com um grande número de threads ao mesmo tempo. A capacidade de processar vários cálculos ao mesmo tempo, torna a GPU uma opção melhor para treinar algoritmos de rede neural. As redes neuronais que são usadas para deepfakes são chamadas de GANs (Generative Adversial Networks), onde dois modelos de aprendizado de máquina concorrem entre si. O primeiro modelo é chamado de gerador que lê os dados reais e gera dados falsos. O segundo modelo chamado discriminador é responsável por detectar os dados falsos. O gerador gera dados falsos até que o discriminador não possa mais detectá-los. Quanto mais dados de treinamento estiverem disponíveis para o gerador, mais fácil será para ele gerar uma deepfake credível.

## O que é Faceswap?

A plataforma mais conhecida e difundida atualmente para a geração de deepfakes é a [Faceswap](https://faceswap.dev/). É uma plataforma de código aberto baseada em Tensorflow, Keras e Python e pode ser executada no Windows, macOS e Linux. Uma comunidade ativa se formou, que troca informações sobre o Github e outros fóruns e compartilha scripts e sugeriu soluções para perguntas.

Você pode executar suas cargas de trabalho deepfake usando o Oracle Free Tier que consiste na Oferta Always Free e uma avaliação gratuita de 30 dias. No Always Free Tier, o VM.Standard.E2.1. Micro pode ser utilizado e, na avaliação de 30 dias, todas as instâncias de VM e BMs disponíveis podem ser testadas. Se você quiser testar a oferta de GPU, poderá fazer upgrade da sua conta do Oracle Cloud para PAYG.

## **Confirmação**

*   **Criado por/Data** - Maria Patelkou, Arquiteto de Soluções HPC, programa Oracle Proposal to Production, março de 2021
*   **Data da Última Atualização** - Maria Patelkou, Arquiteto de Soluções HPC, programa Proposta para Produção da Oracle, março de 2021