# Adicionar Instalador do Gromacs ao Serviço Object Storage

## Introdução

Neste laboratório, você adicionará o Instalador do Gromacs ao Object Storage.

Tempo Estimado de Laboratório: 5 minutos

## Tarefa: Adicionar Instalador do Gromacs ao Object Storage

_Observe que os scripts terraform [arquivo zip](https://github.com/oci-hpc/oci-hpc-runbook-gromacs/tree/master/Resources/gromacs-2020.1.zip) fornecidos neste github já contêm URLs de armazenamento de objetos para Gromacs 2020.1, VMD 1.9.3 e um exemplo de modelo de benchmarking do [Max Planck Institute](https://www.mpibpc.mpg.de/grubmueller/bench). Se você quiser alterar esses urls, modifique o arquivo variable.tf e substitua os valores de gromacs\_url, model\_url e visualizer\_url pelos seus próprios urls de solicitação pré-autenticada._

1.  Selecione o menu ![](./images/menu.png) na parte superior esquerda e, em seguida, selecione Object Storage --> Object Storage
2.  Crie um novo bucket ou selecione um existente. Para criar um, clique em![](./images/create_bucket.png)
3.  Deixe as opções padrão: Padrão como camadas de Armazenamento e chaves Gerenciadas pela Oracle. Clique em![](./images/create_bucket.png)
4.  Clique no nome do bucket recém-criado e selecione![](./images/upload_object.png)
5.  Selecione o arquivo tar do instalador Gromacs e clique![](./images/upload_object.png)
6.  Clique nos 3 pontos no lado direito do objeto cujo upload você acabou de fazer e selecione "Criar Solicitação Pré-Autenticada"
7.  No menu a seguir, deixe as opções padrão e selecione uma data de expiração para o URL do instalador. Clique em![](./images/pre-auth.png)
8.  Na próxima janela, copie o "URL DE SOLICITAÇÃO PRÉ-AUTHENTICADA" e mantenha-o. Você não poderá recuperá-lo depois de fechar esta janela. Se você soltá-lo ou ele expirar, será sempre possível recriar outra Solicitação Pré-autenticada que gerará outro URL

## Confirmação

*   **Autor** - Equipe de Computação de Alto Desempenho
*   **Contribuidores** - Chris Iwicki, Harrison Dvoor, Gloria Lee, Selene Song, Bre Mendonca, Samrat Khosla
*   **Data da Última Atualização** - Samrat Khosla, Outubro de 2020