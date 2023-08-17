## **Exemplo de Benchmark**

![](images/lemans.png " ")

Os desempenhos de STAR-CCM+ são frequentemente medidos usando o benchmark LeMans com células de 17 e 105 Milhões. Os próximos gráficos mostram como o uso de mais nós afeta o runtime, com um dimensionamento muito próximo de 100%. A rede RDMA, que não foi discutida neste runbook, só começa a diferenciar versus execuções TCP regulares se a proporção Células / Núcleo começar a diminuir.

**17 milhões de células**

![](images/RunTime_17M.png " ")

**105 milhões de células**

![](images/Scaling_105M.png " ")

## Confirmação

*   **Autor** - Equipe de Computação de Alto Desempenho
*   **Contribuidores** - Chris Iwicki, Harrison Dvoor, Gloria Lee, Selene Song, Bre Mendonca, Samrat Khosla
*   **Data da Última Atualização** - Samrat Khosla, Outubro de 2020