# Preparar Configuração

## Introdução

Este laboratório mostrará como fazer download do arquivo zip da pilha do Oracle Resource Manager (ORM) necessário para configurar o recurso necessário para executar este workshop.

_Tempo Estimado do Laboratório:_ 10 minutos

### Objetivos

*   Fazer download da pilha do ORM
*   Configurar uma VCN (Virtual Cloud Network) existente

### Pré-requisitos

Este laboratório pressupõe que você tenha:

*   Uma conta no Oracle Cloud

## Tarefa 1: Fazer download do arquivo zip da pilha do Oracle Resource Manager (ORM)

1.  Clique no link abaixo para fazer download do arquivo zip do Resource Manager necessário para criar seu ambiente:
    
    _Observação 1:_ Se estiver fornecendo um único download de Pilha para o workshop, use esta expressão simples.
    
    *   [exemplo-mkplc-freetier.zip](https://objectstorage.us-ashburn-1.oraclecloud.com/p/clKCvIhItftqqFoXdipFq5oukh0jVuwcKEMHVdqOPXN7oUqaoGwPZsimi5pgpfpR/n/natdsecurity/b/stack/o/sample-mkplc-freetier.zip)
    
    _Observação 2:_ Se estiver fornecendo vários Stacks para download no mesmo workshop, use uma expressão condicional semelhante à abaixo. Lembre-se de que a condição ou o _tipo_ deve ser pareado com uma entrada válida no arquivo _manifest.json_. Consulte _freetier-advanced_ e _freetier-basics_
    

\- \[sample-mkplc-advanced.zip\](https://objectstorage.us-ashburn-1.oraclecloud.com/p/\_EIwsXv5v6KkKcQldUQixExqAgJCbY826XovJec4I25rc4dHEZW4whrF-nb2QUye/n/natdsecurity/b/stack/o/sample-mkplc-advanced.zip) \- \[sample-mkplc-basics.zip\](https://objectstorage.us-ashburn-1.oraclecloud.com/p/m4wcgeN1hw9D1zV3pgOkbRjwanAt5dIW7QsZS7znZNnHU63vh495UHhkiRtaDJHE/n/natdsecurity/b/stack/o/sample-mkplc-basics.zip)

2.  Salve em sua pasta de downloads.

É altamente recomendável usar essa pilha para criar uma VCN independente/dedicada com sua(s) instância(s). Vá para a _Tarefa 3_ para seguir nossas recomendações. Se preferir usar uma VCN existente, vá para a próxima tarefa para atualizar sua VCN existente com as regras de Entrada necessárias.

## Tarefa 2: Adicionando regras de segurança a uma VCN existente

Este workshop requer que um determinado número de portas esteja disponível, um requisito que pode ser atendido usando a execução de pilha padrão do ORM que cria uma VCN dedicada. Para usar uma VCN/sub-rede existente, as regras a seguir devem ser adicionadas à lista de segurança.

| Tipo | Porta de Origem | CIDR de Origem | Porta de Destino | Protocolo | Descrição: |
| :-- | :-: | :-: | :-: | :-: | :-- |
| Entrada | Todos | 0.0.0.0/0 | 22 | TCP | SSH |
| Entrada | Todos | 0.0.0.0/0 | 80 | TCP | Área de Trabalho Remota usando noVNC |
| Saída | Todos | N/D | 80 | TCP | Acesso HTTP de saída |
| Saída | Todos | N/D | 443 | TCP | Acesso HTTPS de saída |
| {: title="Lista de Regras de Segurança de Rede Necessárias"} |  |  |  |  |  |

1.  Vá para _Networking >> Virtual Cloud Networks_
2.  Escolha a rede
3.  Em Recursos, selecione Listas de Segurança
4.  Clique em Listas de Segurança Padrão no botão Criar Lista de Segurança
5.  Clique no botão Add Ingress Rule
6.  Informe o seguinte:
    *   Tipo de Origem: CIDR
    *   CIDR de Origem: 0.0.0.0/0
    *   Protocolo IP: TCP
    *   Faixa de Portas de Origem: Todas (Manter Padrão)
    *   Faixa de Portas de Destino: _Selecionar na tabela acima_
    *   Descrição: _Selecione a descrição correspondente na tabela acima_
7.  Clique no botão Adicionar Regras de Entrada
8.  Repita as etapas \[5-7\] até que uma regra seja criada para cada porta listada na tabela

## Tarefa 3: Configurar Computação

Usando os detalhes das duas Tarefas acima, vá até o laboratório _Configuração do Ambiente_ para configurar seu ambiente de workshop usando o Oracle Resource Manager (ORM) e uma das seguintes opções:

*   Criar Pilha: _Computação + Rede_
*   Criar Pilha: _Somente computação_ com uma VCN existente na qual as listas de segurança foram atualizadas de acordo com a _Tarefa 2_ acima

Agora você pode ir para o próximo laboratório.

## Confirmação

*   **Autor** - Rene Fontcha, LiveLabs Líder de Plataforma, Tecnologia NA
*   **Contribuidores** - Meghana Banka
*   **Última Atualização em Por/Data** - Rene Fontcha, LiveLabs Líder de Plataforma, Tecnologia NA, agosto de 2022