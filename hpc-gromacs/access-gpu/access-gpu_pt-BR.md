# Acessar seu Nó GPU

## Introdução

Neste laboratório, você acessará o seu Nó GPU.

Tempo Estimado de Laboratório: 5 minutos

## Tarefa: Acessar seu Nó GPU

1.  Depois que seu job for concluído com sucesso no Resource Manager, você poderá localizar os Endereços IP Públicos do nó GPU e a Chave Privada no menu esquerdo inferior em **Saídas**.
    
2.  Copie a Chave Privada na sua máquina local, altere as permissões da chave e faça log-in na instância:
    
        chmod 400 /home/user/key
        ssh -i /home/user/key opc@ipaddress
        
        
3.  Depois de efetuar log-in no nó GPU, você poderá executar Gromacs em /mnt/block. Consulte o arquivo README.md para obter comandos específicos sobre como executar Gromacs na sua instância de GPU.
    

## Confirmação

*   **Autor** - Equipe de Computação de Alto Desempenho
*   **Contribuidores** - Chris Iwicki, Harrison Dvoor, Gloria Lee, Selene Song, Bre Mendonca, Samrat Khosla
*   **Data da Última Atualização** - Samrat Khosla, Outubro de 2020