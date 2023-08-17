# Pós-processamento

## Introdução

Neste laboratório, você usará o software Visual Molecular Dynamics (VMD) para analisar os modelos para pós-processamento.

Tempo Estimado de Laboratório: 5 minutos

## Tarefa: Pós-processamento

1.  Para pós-processamento, você pode usar o software Visual Molecular Dynamics (VMD) para analisar os modelos. Execute os seguintes comandos para configurar VMD:
    
        <copy>
        ./configure
        cd src
        sudo make install
        </copy>
        
2.  Se você estiver usando vnc, inicie o vncserver e crie uma senha vnc da seguinte forma:
    
        <copy>
        sudo systemctl start vncserver@:1.service
        sudo systemctl enable vncserver@:1.service
        vncserver
        vncpasswd
        </copy>
        
3.  Inicie uma conexão vnc usando localhost:5901 (certifique-se de que o tunelamento esteja configurado) e execute os seguintes comandos para iniciar o VMD:
    
        <copy>
        vmd
        </copy>
        
4.  Abra os arquivos pdb apoa1 e stmv em /mnt/block/work/NAMD\_models e comece a jogar com seus modelos.
    

## Confirmação

*   **Autor** - Equipe de Computação de Alto Desempenho
*   **Contribuidores** - Chris Iwicki, Harrison Dvoor, Gloria Lee, Selene Song, Bre Mendonca, Samrat Khosla
*   **Data da Última Atualização** - Samrat Khosla, Outubro de 2020