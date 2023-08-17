# Introdução ao NAMD

Este Runbook fornece as etapas para implantar uma máquina GPU no Oracle Cloud Infrastructure, instalar NAMD e executar um modelo usando o software NAMD.

NAMD é um software de dinâmica molecular que simula os movimentos de átomos em biomoléculas sob um conjunto predefinido de condições. É utilizado para identificar o comportamento dessas biomoléculas quando expostas a mudanças de temperatura, pressão e outras insumos que mimetizam as condições reais encontradas em um organismo vivo. A NAMD pode ser usada para estabelecer padrões em dobramento de proteínas, ligação proteína-ligante e transporte de membrana celular, tornando-a uma aplicação muito útil para pesquisa e descoberta de drogas.

A NAMD é construída em Charm++ e Converse e pode ser executada em computadores de alto desempenho para executar o processamento paralelo. Foi desenvolvido pela Universidade de Illinois. Mais informações podem ser encontradas [aqui](http://charm.cs.illinois.edu/research/moldyn).

![](./images/protein.gif)

## **Arquitetura**

A arquitetura desse runbook é simples, uma única máquina em execução dentro de uma VCN OCI com uma sub-rede pública. Como uma instância GPU é usada, o armazenamento em blocos é anexado à instância e instalado com o aplicativo NAMD. A instância está localizada em uma sub-rede pública e recebe um ip público, que pode ser acessado por meio do ssh.

![](./images/arch-draft.png)

## Confirmação

*   **Autor** - Equipe de Computação de Alto Desempenho
*   **Contribuidores** - Chris Iwicki, Harrison Dvoor, Gloria Lee, Selene Song, Bre Mendonca, Samrat Khosla
*   **Data da Última Atualização** - Samrat Khosla, Outubro de 2020