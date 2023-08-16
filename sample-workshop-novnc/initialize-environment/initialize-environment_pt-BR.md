# Inicializar Ambiente

## Introdução

Neste laboratório, analisaremos e iniciaremos todos os componentes necessários para executar com sucesso este workshop.

_Tempo Estimado do Laboratório:_ 30 minutos.

### Objetivos

*   Inicialize o ambiente do workshop.

### Pré-requisitos

Este laboratório pressupõe que você tenha:

*   Uma conta no Oracle Cloud
*   Você concluiu:
    *   Laboratório: Preparar Configuração (somente _Camada Grátis_ e _Tenants Pagos_)
    *   Laboratório: Configuração do Ambiente

> **Observação:** _Ao fazer Copiar/Colar usando a função_ **Copiar** _aproveitada usada em todo o guia, você deve pressionar a tecla_ **ENTER** _após colar. Caso contrário, a última linha permanecerá no buffer até que você pressione_ **ENTER!**

## Tarefa 1: Validar se os Processos Necessários estão Ativos e em Execução.

1.  Agora com acesso à sua sessão de área de trabalho remota, continue conforme indicado abaixo para validar seu ambiente antes de começar a executar os laboratórios subsequentes. Os seguintes Processos devem estar ativos e em execução:
    
    *   Listeners de Banco de Dados
        *   LISTENER (1521)
        *   LISTCDB2 (1522)
    *   Instâncias do Servidor de Banco de Dados
        *   CDB1
        *   CDB2
    
    Você pode testar a conectividade do banco de dados clicando no sinal _+_ ao lado do(s) Banco(s) de Dados, conforme mostrado abaixo no painel _Conexões Oracle do SQL Developer_.
    
    ![](./images/19c_hol_landing.png " ")
    
2.  Clique no ícone _Terminal_ na área de trabalho para iniciar uma sessão e execute o seguinte para validar se os processos esperados estão ativos.
    
        <copy>
        ps -ef|grep LIST|grep -v grep
        ps -ef|grep ora_|grep pmon|grep -v grep
        systemctl status oracle-database
        systemctl status oracle-db-listener
        </copy>
        
    
    ![](./images/check-pmon-up.png " ") ![](./images/check-db-service-up.png " ") ![](./images/check-dblistner-service-up.png " ")
    
    Se todos os processos esperados forem mostrados na saída conforme visto acima, seu ambiente estará pronto para a próxima tarefa.
    
3.  Se você vir saída(s) questionável(is), falha ou componente(s) inativo(s), reinicie o serviço adequadamente
    
        <copy>
        sudo systemctl restart oracle-database
        sudo systemctl restart oracle-db-listener
        </copy>
        

## Tarefa 2: Inicializar Banco de Dados para Casos de Uso Multitenant

1.  Em sua sessão de área de trabalho remota como usuário _oracle_, execute o bloco abaixo para atualizar artefatos de laboratórios
    
        <copy>
        cd ~
        wget -O labs-novnc.zip https://objectstorage.us-ashburn-1.oraclecloud.com/p/jyHA4nclWcTaekNIdpKPq3u2gsLb00v_1mmRKDIuOEsp--D6GJWS_tMrqGmb85R2/n/c4u04/b/livelabsfiles/o/labfiles/labs-novnc.zip
        unzip -qo labs-novnc.zip
        rm -f labs-novnc.zip
        cd labs/multitenant
        chmod +x *.sh
        </copy>
        
    
    ![](./images/init-multitenant.png " ")
    

Agora você pode [acessar o próximo laboratório](#next).

## Apêndice 1: Gerenciando Serviços de Inicialização

1.  Serviço de banco de dados (Todos os bancos de dados e Listener Padrão).
    
    *   Iniciar
    
        <copy>
        sudo systemctl start oracle-database
        </copy>
        
    
    *   Parar
    
        <copy>
        sudo systemctl stop oracle-database
        </copy>
        
    
    *   Status
    
        <copy>
        systemctl status oracle-database
        </copy>
        
    
    *   Reiniciar
    
        <copy>
        sudo systemctl restart oracle-database
        </copy>
        
2.  Serviço de banco de dados ( Listeners Não Padrão).
    
    *   Iniciar
    
        <copy>
        sudo systemctl start oracle-db-listener
        </copy>
        
    
    *   Parar
    
        <copy>
        sudo systemctl stop oracle-db-listener
        </copy>
        
    
    *   Status
    
        <copy>
        systemctl status oracle-db-listener
        </copy>
        
    
    *   Reiniciar
    
        <copy>
        sudo systemctl restart oracle-db-listener
        </copy>
        

## Confirmação

*   **Autor** - Andy Rivenes, Gerente Sênior de Produtos, Oracle Database In-Memory
*   **Contribuidores** - Kay Malcolm, Didi Han, Rene Fontcha
*   **Última Atualização em Por/Data** - Rene Fontcha, LiveLabs Líder de Plataforma, Tecnologia NA, Outubro de 2021