# Workshop com um único conjunto de laboratórios

## Instruções - Exclua este arquivo quando terminar

1.  Abra o modelo de amostra-workshop no Atom ou Visual Studio Code
2.  Pré-criamos 5 pastas. Um workshop é criado com base em vários laboratórios.
3.  Remova os comentários como este: _Listar objetivos para este laboratório_
4.  Certifique-se de usar a pasta minúscula e o nome do arquivo e traços para espaços (setup-adb NOT Setup\_ADB)
5.  Seus nomes de imagem devem ter nomes descritivos. Não apenas adb1, adb2, adb3. Para acessibilidade com deficiência, precisamos das descrições das imagens para explicar a aparência da imagem. Lembre-se de todas as letras minúsculas e traços.
6.  Baixe nosso documento de controle de qualidade do WMS. Encontramos workshops mais rápidos quando você sabe o que é necessário para passar para a produção e usar o esqueleto.

PS: Você não precisa de um Readme.md. O arquivo Readme só existe nos níveis superiores da biblioteca. Direcionamos todo o tráfego para LiveLabs, pois não podemos rastrear o uso em GitHub. Não crie links diretos para GitHub, seu workshop pode ser super popular, mas não podemos rastreá-lo para que ninguém saiba.

## Caminho Absoluto para Navegação no menu do Oracle Cloud

**Lab 1: Provisionar uma Instância -> Etapa 0: Use estas Fotos Padronizadas para Navegação no Oracle Cloud (Comumente para Provisionamento)** - Incluímos uma lista de capturas de tela comuns para navegar no Menu do Oracle Cloud. Leia esta seção e use as imagens de caminho absoluto relevantes, quando apropriado. Isso comprovará seu workshop no caso de atualizações da interface do usuário do Oracle Cloud.

## Estrutura da Pasta

Neste exemplo, o objetivo é criar vários workshops "filhos" de um workshop mais "pai". Os filhos são compostos por partes do pai.

amostra-workshop/--laboratórios individuais

        provision/
        setup/
        dataload/
        query/
        introduction/
          introduction.md       -- description of the everything workshop, note that it is a "lab" since there is only one
    
    workshops/
       freetier/                -- freetier version of the workshop
        index.html
        manifest.json
       livelabs/                -- livelabs version of the workshop
        index.html
        manifest.json
    

### FreeTier vs LiveLabs

*   "FreeTier" - inclui Testes Gratuitos, Contas Pagas e, para alguns workshops, contas Always Free (botão marrom)
*   "LiveLabs" - são workshops que usam tenancies fornecidas pela Oracle (botão verde)
*   "Desktop" - esta é uma nova implantação na qual o workshop é encapsulado em um ambiente NoVNC em execução em uma instância de computação

### Sobre o Workshop

O workshop inclui todos os 6 laboratórios individuais em uma única sequência.

A estrutura de pastas inclui um "lab" de Introdução que descreve o workshop como um conjunto completo de 6 laboratórios. Observação: talvez você não precise ter uma introdução diferente para cada uma das versões pai e filho dos workshops, isso é apenas ilustrativo.

Verifique a pasta product-name-workshop/freetier e examine o arquivo manifest.json para ver a estrutura.

> **Observação:** O uso de "Lab n:" nos títulos é opcional

O "lab" de Pré-requisito é o primeiro laboratório em uma pasta comum no repositório oracle/learning-library. Como esse laboratório já existe, podemos usar um RAW/URL absoluto:

    "filename": "https://oracle-livelabs.github.io/common/labs/cloud-login/cloud-login-livelabs2.md"        },
    

O arquivo manifest.json precisa saber a localização de cada laboratório em relação ao local em que ele existe na hierarquia. Nesta estrutura, os laboratórios estão localizados em dois níveis acima, por exemplo:

    "filename": "../../provision/provision.md"
    

### Por exemplo:

Este [Workshop do APEX](https://oracle.github.io/learning-library/developer-library/apex/spreadsheet/workshops/freetier/) é um bom exemplo de um workshop com um único conjunto de laboratórios: [https://github.com/oracle-livelabs/apex/tree/main/spreadsheet](https://github.com/oracle-livelabs/apex/tree/main/spreadsheet).