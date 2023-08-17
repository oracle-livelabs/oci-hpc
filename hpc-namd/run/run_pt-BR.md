# Executando a Aplicação

## Introdução

Neste laboratório, você executará o aplicativo.

Tempo Estimado de Laboratório: 5 minutos

## Tarefa: Executando o Aplicativo

Se os scripts terraform fornecidos forem usados para iniciar o aplicativo, o NAMD com CUDA será instalado na pasta /mnt/block/NAMD/NAMD\_2.13\_CUDA e dois modelos de exemplo estarão disponíveis na pasta /mnt/block/work/NAMD\_models.

1.  Execute NAMD com CUDA por meio do seguinte comando:
    
        <copy>
        
        namd2 +p<# of cores> +setcpuaffinity +devices <cuda visible devices> +idlepoll +commap <CPU to GPU mapping> <model path> > output.txt
        
        </copy>
        
        
    
    onde: - +p - número de núcleos de CPU - +setcpuaffinity - atribuir threads/processos de maneira round-robin a núcleos disponíveis na ordem em que são numerados pelo sistema operacional - +devices - número de dispositivos GPU - +idlepoll - sondar a GPU por resultados em vez de dormir enquanto ociosa - +commap - mapeamento de comunicação da CPU com o arquivo de saída da GPU - output.txt - com análise
    
    Exemplo de BM.GPU2.2:
    
        <copy>
        
        namd2 +p26 +devices 0,1 +idlepoll +commap 0,14 /mnt/block/work/NAMD_models/apoa1/apoa1.namd > output.txt
        
        </copy>
        
    
    Exemplo de BM.GPU3.8:
    
        <copy>
        
        namd2 +p52 +devices 0,1,2,3,4,5,6,7 +commap 0,1,2,3,26,27,28,29 /mnt/block/work/NAMD_models/apoa1/apoa1.namd > output.txt
        
        </copy>
        
2.  Execute ns\_per\_day.py no arquivo de saída para calcular nanossegundos por dia, em média, nas instruções de Tempo registradas. Usado para identificar o desempenho e a eficiência do aplicativo usando a configuração atual de CPU/GPU.
    
        <copy>
        
        ns_per_day.py output.txt
        </copy>
        

## Confirmação

*   **Autor** - Equipe de Computação de Alto Desempenho
*   **Contribuidores** - Chris Iwicki, Harrison Dvoor, Gloria Lee, Selene Song, Bre Mendonca, Samrat Khosla
*   **Data da Última Atualização** - Samrat Khosla, Outubro de 2020