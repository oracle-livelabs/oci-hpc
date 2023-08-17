# Selecionar Variáveis

## Introdução

Neste laboratório, você preencherá as variáveis da sua pilha.

Tempo Estimado de Laboratório: 10 minutos

## Tarefa: Selecionar Variáveis

1.  Clique em ![](./images/next.png) e preencha as variáveis.
    
    **Nó GPU**:
    
    *   SHAPE OF THE GPU COMPUTE NODE: Forma do Nó de Computação que será usado para executar Gromacs. Selecione as formas bare metal BM.GPU2.2 ou BM.GPU3.8 para obter melhor desempenho.
    *   DOMÍNIO DE DISPONIBILIDADE: AD da Instância e do Volume em Blocos. O AD deve ter GPUs disponíveis.
    *   Contagem de Nós de GPU: Número de Nós de GPU Obrigatórios.
    *   VNC TYPE FOR THE GPU: Tipo de Visualização para o headnode: none, VNC ou X11VNC.
    *   SENHA DAS SESSÕES DA VNC: senha para usar a sessão VNC no Pré/Pós-Nó.
    
    **Opções de Bloco**:
    
    *   TAMANHO DE VOLUME DE BLOQUEIO ( GB ): Tamanho do volume em blocos compartilhado.
    
    **Gromacs**:
    
    *   URL TO DOWNLOAD Gromacs: URL dos binários compilados Gromacs 2020.1 (substitua o url por uma versão diferente ou deixe em branco se quiser baixar mais tarde).
    *   URL TO DOWNLOAD A MODEL TARBALL: URL do modelo que você deseja executar (substitua o url por um modelo diferente ou deixe em branco se você deseja fazer download posteriormente).
    *   URL TO DOWNLOAD VMD VISUALIZATION SOFTWARE: URL para fazer download do VMD 1.9.3 para visualizar modelos Gromacs (substitua o url por outro software de visualização ou deixe em branco se quiser fazer download posteriormente).
2.  Clique em ![](./images/next.png). Verifique as informações e clique em ![](./images/create.png)
    

## Confirmação

*   **Autor** - Equipe de Computação de Alto Desempenho
*   **Contribuidores** - Chris Iwicki, Harrison Dvoor, Gloria Lee, Selene Song, Bre Mendonca, Samrat Khosla
*   **Data da Última Atualização** - Samrat Khosla, Outubro de 2020