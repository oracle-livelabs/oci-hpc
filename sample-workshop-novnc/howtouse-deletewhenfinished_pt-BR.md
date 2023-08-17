# Workshop com um único conjunto de laboratórios

## Instruções - Exclua este arquivo quando terminar

1.  Abra o modelo de amostra-workshop no Atom ou Visual Studio Code
2.  Pré-criamos 5 pastas. Um workshop é criado com base em vários laboratórios.
3.  Remova os comentários como este: _Listar objetivos para este laboratório_
4.  Certifique-se de usar a pasta minúscula e o nome do arquivo e traços para espaços (setup-adb NOT Setup\_ADB)
5.  Seus nomes de imagem devem ter nomes descritivos. Não apenas adb1, adb2, adb3. Para acessibilidade com deficiência, precisamos das descrições das imagens para explicar a aparência da imagem. Lembre-se de todas as letras minúsculas e traços.
6.  Faça o download do documento de Feedback de QA no WMS. Encontramos workshops mais rápidos quando você sabe o que é necessário para passar para a produção e usar o esqueleto.

PS: Você não precisa de um Readme.md. O arquivo Readme só existe nos níveis superiores da biblioteca. Direcionamos todo o tráfego para LiveLabs, pois não podemos rastrear o uso em GitHub. Não crie links diretos para GitHub, seu workshop pode ser super popular, mas não podemos rastreá-lo para que ninguém saiba.

## Caminho Absoluto para Navegação no menu do Oracle Cloud

**Lab 1: Provisionar uma Instância -> Etapa 0: Use estas Fotos Padronizadas para Navegação no Oracle Cloud (Comumente para Provisionamento)** - Incluímos uma lista de capturas de tela comuns para navegar no Menu do Oracle Cloud. Leia esta seção e use as imagens de caminho absoluto relevantes, quando apropriado. Isso comprovará seu workshop no caso de atualizações da interface do usuário do Oracle Cloud.

## Estrutura da Pasta

Neste exemplo, o objetivo é criar vários workshops "filhos" de um workshop mais "pai". Os filhos são compostos por partes do pai.

*   amostra-workshop/
    *   laboratórios individuais
        *   prepare-setup/ -- contém o link para fazer download da pilha do ORM/Terraform do workshop
        *   initialize-environment/ -- tarefas para inicializar o ambiente após o Provisionamento
        *   provisionar/ -- primeiro laboratório fictício
        *   dataload/ -- segundo laboratório ficcional
        *   query/ -- terceiro laboratório fictício
        *   introdução/
            *   introduction.md -- descrição do workshop de tudo, observe que é um "lab" porque há apenas um
    *   workshops/
        *   desktop/ -- versão _obrigatória_ da área de trabalho do workshop (todos os laboratórios)
            *   index.html
            *   manifest.json
        *   desktop-advanced/ -- versão desktop do workshop (introdução, inicialização-ambiente, carga de dados, consulta)
            *   index.html
            *   manifest.json
        *   desktop-basics/ -- versão desktop do workshop (introdução, inicialização-ambiente, provisão)
            *   index.html
            *   manifest.json
        *   tenancy/ -- versão da tenancy _obrigatória_ do workshop (todos os laboratórios)
            *   index.html
            *   manifest.json
        *   tenancy-advanced/ -- versão da tenancy do workshop (introdução, inicialização-ambiente, carga de dados, consulta)
            *   index.html
            *   manifest.json
        *   tenancy-basics/ -- versão da tenancy do workshop (introdução, inicialização-ambiente, provisão)
            *   index.html
            *   manifest.json
        *   sandbox/ -- versão de sandbox _obrigatória_ do workshop (todos os laboratórios)
            *   index.html
            *   manifest.json
        *   sandbox avançado/ -- versão sandbox do workshop (introdução, inicialização-ambiente, carga de dados, consulta)
            *   index.html
            *   manifest.json
        *   sandbox-basics/ -- versão de sandbox do workshop (introdução, inicialização-ambiente, provisão)
            *   index.html
            *   manifest.json

### tenancy vs sandbox vs desktop

*   "desktop" - Guia do workshop exibido dentro da sessão de área de trabalho remota noVNC _obrigatória_
*   "tenancy" - Workshop provisionado usando a tenancy do cliente _obrigatório_
*   "sandbox" - Workshop provisionado usando "Reserve Workshop on LiveLabs Sandbox" (botão verde) _obrigatório_
*   "desktop-xxxxxxx" - Guia de subconjunto ou mini-workshop exibido dentro da sessão de área de trabalho remota noVNC
*   "tenancy-xxxxxxx" - Subconjunto ou mini-workshop provisionado usando a tenancy do cliente (botão marrom)
*   "sandbox-xxxxxxx" - Subconjunto ou mini-workshop provisionado usando "Reserve Workshop on LiveLabs Sandbox" (botão verde)

### Sobre o Workshop

O workshop inclui todos os 6 laboratórios individuais em uma única sequência.

A estrutura de pastas inclui um "lab" de Introdução que descreve o workshop como um conjunto completo de 6 laboratórios. Observação: talvez você não precise ter uma introdução diferente para cada uma das versões pai e filho dos workshops, isso é apenas ilustrativo.

Verifique a pasta product-name-workshop/tenancy e examine o arquivo manifest.json para ver a estrutura.

> **Observação:** O uso de "Lab n:" nos títulos é opcional

O "lab" de Pré-requisito é o primeiro laboratório em uma pasta comum no repositório oracle/learning-library. Como esse laboratório já existe, podemos usar um RAW/URL absoluto:

    "filename": "https://oracle-livelabs.github.io/common/labs/cloud-login/cloud-login-livelabs2.md"        },
    

O arquivo manifest.json precisa saber a localização de cada laboratório em relação ao local em que ele existe na hierarquia. Nesta estrutura, os laboratórios estão localizados em dois níveis acima, por exemplo:

    "filename": "../../provision/provision.md"
    

### Por exemplo:

Este [Workshop do APEX](https://oracle-livelabs.github.io/apex/spreadsheet/) é um bom exemplo de um workshop com um único conjunto de laboratórios: [https://github.com/oracle-livelabs/apex/tree/main/spreadsheet](https://github.com/oracle-livelabs/apex/tree/main/spreadsheet).