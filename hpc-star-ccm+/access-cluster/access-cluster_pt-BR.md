# Acesse seu Cluster

## Introdução

Neste laboratório, você acessará seu cluster.

Tempo Estimado de Laboratório: 10 minutos

## Tarefa: Acesse seu Cluster

1.  O endereço IP público do bastion pode ser encontrado no menu esquerdo inferior em Saídas. Se você navegar até suas instâncias no menu principal, também encontrará sua instância bastion e o IP público.
    
2.  A Chave Privada para acessar as máquinas também pode ser encontrada lá. Copie o texto em um arquivo em sua máquina, vamos dizer/home/user/key:
    
        chmod 600 /home/user/key 
        ssh -i /home/user/key opc@ipaddress 
        

## Confirmação

*   **Autor** - Equipe de Computação de Alto Desempenho
*   **Contribuidores** - Chris Iwicki, Harrison Dvoor, Gloria Lee, Selene Song, Bre Mendonca, Samrat Khosla
*   **Data da Última Atualização** - Samrat Khosla, Outubro de 2020