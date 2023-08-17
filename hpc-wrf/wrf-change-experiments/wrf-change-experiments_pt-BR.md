# Alterando Dados de Experiência com o WRF

## Introdução

Este é um guia menor em comparação com o WRF Setup, Installation e Run Guide. Ele deve ser usado depois que você tiver concluído o WRF Setup, Installation, and Run Guide ou usado o link para download para começar a usar uma imagem personalizada com o WRF 4.1.5 e o WPS 4.1 já configurados.

Tempo Estimado de Laboratório: 45 minutos

### Objetivos

Neste laboratório, você vai aprender sobre:

*   Criando domínio WRF com Geogrid
*   Fazendo download de dados GFS e executando Ungrib e Metgrid
*   Executando WRF com dados reais

### Pré-requisitos

Este laboratório pressupõe que você tenha:

*   Uma conta no Oracle Free Tier ou Paid Cloud
*   Acesso a uma instância de CPU do Ubuntu 18.04, conforme abordado nos laboratórios 1 e 2
    *   Observe que neste laboratório vamos usar uma **forma VM.Standard2.16**. Você pode usar uma forma menor se desejar.
*   Já passou pelo WRF Setup, Installation, and Run Guide ou baixou a imagem personalizada fornecida ou [aqui](https://objectstorage.us-ashburn-1.oraclecloud.com/p/lRqMYYN5VTdSgBz9f8nv7Tz5mzMGMqr7wlN2Y_q6g6GHmTdc9GX8lokgmTui81BA/n/hpc_limited_availability/b/Demo_Materials/o/WRF_DEMOV2).
    *   **Observação**: A senha do Ubuntu é ppkkego e a senha do vnc é ppkkego  
        Elas podem ser alteradas com os seguintes comandos no terminal remoto:
        
            <copy>
            sudo passwd ubuntu
            sudo vncpasswd
            </copy> 
            

## Tarefa 1: Estabelecendo conexão com a Área de Trabalho do Gnome

Usaremos o [TigerVNC Viewer](https://tigervnc.org/) para estabelecer conexão com nossa instância.

1.  Abrir um terminal local
    
    Execute o seguinte comando com informações pertencentes à sua instância: ssh -L 5901:localhost:5901 ubuntu@IPADDRESS ![Captura de tela de ssh](images/ssh.png)
    
2.  Iniciar sessão vncserver  
    vncserver  
    ![Captura de tela de ssh](images/vncstart.png)
    
3.  Abrir TigerVNC Viewer
    
    Servidor VNC: localhost:1  
    Em seguida, clique em conectar  
    ![Captura de tela do Visualizador TigerVNC](images/tigervnc.png)
    
4.  Digite sua senha da VNC  
    Em seguida, clique em OK  
    ![Captura de tela da autenticação VNC](images/vncauth.png)
    
5.  Você será finalmente apresentado a um desktop gnome e deverá continuar trabalhando aqui.  
    ![Captura de tela do Gnome Desktop](images/gnomedesktop.png)
    

## Tarefa 2: Criando Área Geográfica / Domínio

1.  Precisamos ajustar o arquivo namelist.wps para zero em um local de escolha. Será uma pequena grade (10.000 x 10.000 metros) com Houston Texas USA como ponto central.
    
        <copy>
        cd ~/WRF/WPS-4.1
        vi namelist.wps
        </copy>
        
    
    **Altere todos os valores abaixo para sua preferência com base em sua experiência**.
    
    *   `max_dom` Um inteiro especificando o número total de domínios, incluindo o domínio pai, na simulação. O valor padrão é 1.
    *   `e_we`: Inteiros que especificam a dimensão oeste-leste completa da grade. Nenhum valor padrão.
    *   `e_sn`: Inteiros que especificam a dimensão sul-norte completa da grade. Nenhum valor padrão.
    *   `dx`: Um valor real especificando a distância da grade (em metros) na direção x em que o fator de escala do mapa é 1.
    *   `dy`: Um valor real especificando a distância da grade (em metros) na direção y em que o fator de escala do mapa é 1.
    *   `ref_lat`: Um valor real especificando a parte de latitude de uma localização (latitude, longitude) cuja localização (i,j) no domínio de simulação é conhecida.
    *   `ref_lon`: Um valor real especificando a parte de longitude de um local (latitude, longitude) cujo local (i, j) no domínio de simulação é conhecido.
    *   `truelat1`: Um valor real especificando a primeira latitude verdadeira para a projeção conformal de Lambert, ou a única latitude verdadeira para as projeções Mercator e estereográfica polar.
    *   `truelat2`: Um valor real especificando a segunda latitude verdadeira para a projeção cônica conformal de Lambert.
    *   `stand_lon`: Um valor real especificando a longitude que é paralela ao eixo y nas projeções estereográficas conformais e polares de Lambert. Para a projeção regular de latitude-longitude, esse valor dá a rotação sobre os pólos geográficos da terra.
    *   `geog_data_path`: Uma string de caracteres que fornece o caminho, relativo ou absoluto, para o diretório no qual os diretórios de dados geográficos podem ser encontrados.
    
        <copy>
        &share  
        wrf_core = 'ARW',  
        max_dom = 1,  
        start_date = '2006-08-16_12:00:00','2006-08-16_12:00:00',  
        end_date   = '2006-08-16_18:00:00','2006-08-16_12:00:00',  
        interval_seconds = 21600  
        io_form_geogrid = 2,  
        /  
        &geogrid  
        parent_id         =   1,   1,  
        parent_grid_ratio =   1,   3,  
        i_parent_start    =   1,  31,  
        j_parent_start    =   1,  17,  
        e_we              =  100, 112,  
        e_sn              =  100,  97,  
        !  
        !!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!! IMPORTANT NOTE !!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!  
        ! The default datasets used to produce the MAXSNOALB and ALBEDO12M  
        ! fields have changed in WPS v4.0. These fields are now interpolated  
        ! from MODIS-based datasets.  
        !  
        ! To match the output given by the default namelist.wps in WPS v3.9.1,  
        ! the following setting for geog_data_res may be used:  
        !  
        ! geog_data_res = 'maxsnowalb_ncep+albedo_ncep+default','maxsnowalb_ncep+albedo_ncep+default',  
        !  
        !!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!! IMPORTANT NOTE !!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!  
        !  
        geog_data_res = 'default','default',  
        dx = 10000,  
        dy = 10000,  
        map_proj = 'lambert',  
        ref_lat   =  41.87,  
        ref_lon   = -87.62,  
        truelat1  =  41.87,  
        truelat2  =  41.87,  
        stand_lon = -87.62,  
        geog_data_path = '~/WRF/GEOG/'  
        /  
        </copy>
        
    
    **Para sair, basta pressionar esc e depois mudar: seguido por wq enter**  
    **Todas essas mudanças são para uma área geográfica com Houston TX como o epicentro.**
    
2.  Agora que colocamos as informações para nossa área geográfica de interesse (Este guia usa Houston TX USA como ponto central) permite usar ncview para verificar se temos a localização correta após executar o programa geogrid.
    
        <copy>
        ./geogrid.exe
        ncview geo_em.d01.nc
        </copy>
        
3.  Use a var 2d para verificar a máscara de terra para verificar a localização. Se estiver satisfeito com a imagem, concluímos a criação de domínio.
    
    ![Captura de tela do ncview](images/nc21.png)
    

## Tarefa 3: Adicionando Dados Meteorológicos à Área Geográfica / Domínio

Depois de configurar nossa área geográfica ou domínio. Agora precisamos obter dados meteorológicos para sobrepor ao domínio.

1.  Em um Web browser, navegue até https://nomads.ncep.noaa.gov/pub/data/nccf/com/gfs/prod/
    
2.  Clique no vínculo com a última data (ex gfs.20201120/ como hoje é 20 de novembro de 2020)
    
3.  Escolha 00  
    `Don’t actually download anything.` Criaremos um script para isso. Vamos reduzir a resolução para facilitar o processamento de dados neste laboratório; portanto, 0p50 em vez de 0p25. Use 0p25 se você tiver armazenamento adicional e quiser dados de maior resolução/mais confiáveis. É necessário fazer download do número de arquivos durante o tempo que você deseja executar. Cada arquivo tem uma hora de dados em determinado intervalo. EX se você quiser executar por seis horas, precisará de gfs.t00z.pgrb2.0p50.f000, gfs.t00z.pgrb2.0p50.f003 e gfs.t00z.pgrb2.0p50.f006. 0p25 estará em uma hora de etapas e 0p50 estará em 3 horas de etapas. Para o tutorial, usaremos apenas 6 horas de dados. Sinta-se à vontade para usar mais à medida que se tornar mais confortável usando WRF mais tarde.
    
4.  Permite navegar até o diretório correto e editar o script pré-criado para fazer download dos dados.
    
        <copy>
        cd ~/WRF
        cd scripts
        vi download_gfs.sh
        </copy>
        
    
    O script será o seguinte:  
    Você só precisa editar `year`, `month` e `day` para os dados dos quais deseja fazer download.
    
        <copy>
        #!/bin/bash  
        
        inputdir=/home/ubuntu/WRF/GFS  
        rm -rf $inputdir  
        mkdir $inputdir  
        
        year=2020  
        month=12  
        day=09  
        cycle=00  
        
        for ((i=000; i<=006; i+=3))  
        do  
                ftime=`printf "%03d\n" "${i}"`  
                server=https://nomads.ncep.noaa.gov/pub/data/nccf/com/gfs/prod  
                directory=gfs.${year}${month}${day}/${cycle}  
                file=gfs.t${cycle}z.pgrb2.0p50.f${ftime}  
                url=${server}/${directory}/${file}  
                echo $url  
                wget -O ${inputdir}/${file} ${url}  
        done
        </copy>  
        
    
    **Para sair, basta pressionar Esc e, em seguida, Shift: seguido por wq enter**  
    **Este script fará download de SIX horas de dados para a data 12/09/20 na resolução 0p50. Por favor, ajuste-se para atender às suas necessidades.**
    
5.  Os seguintes comandos tornam o script executável e o executam para fazer download dos dados:
    
        <copy>
        chmod +x download_gfs.sh
        ./download_gfs.sh
        </copy>
        
6.  Agora que baixamos nossos dados, vamos passar pelo processo de sobreposição sobre nosso domínio. Vamos usar ungrib e metgrid para fazer isso. Isso será dividido em duas seções: uma seção de configuração e execução.
    
    **Configurar:**
    
        <copy>
        cd ~/WRF/WPS-4.1
        rm ./Vtable
        ln -s ungrib/Variable_Tables/Vtable.GFS ./Vtable
        ./link_grib.csh ~/WRF/GFS/
        vi namelist.wps 
        </copy> 
        
    
    **Altere todos os valores abaixo com base nos arquivos baixados**.
    
    *   `start_date` no formato: Ano-Mês-Dia\_hora:minuto:segundo
    *   `end_date` no formato: Ano-Mês-Dia\_hora:minuto:segundo
    *   `interval seconds`: O número inteiro de segundos entre arquivos de entrada meteorológicos que variam de tempo. Nenhum valor padrão.
    
        <copy>
        &share  
        wrf_core = 'ARW',  
        max_dom = 1,  
        start_date = '2020-12-09_00:00:00',         # start time  
        end_date   = '2020-12-09_06:00:00',         #6 hours later than start time  
        interval_seconds = 10800                    #3 hours worth of seconds interval between steps  
        io_form_geogrid = 2,  
        /
        </copy>
        
    
    **Para sair, basta pressionar esc, em seguida, turno: seguido por wq entrar**
    
    **Executar:**
    
        <copy>
        ./ungrib.exe
        rm ./METGRID.TBL 
        ln -s metgrid/METGRID.TBL.ARW ./METGRID.TBL
        ./metgrid.exe
        </copy>
        
7.  Podemos observar os resultados usando o ncview e examinar o skintemp para ver o valor dos dados baixados.
    
        <copy>
        ncview met_em.d01.2020-12-09_00\:00\:00.nc 
        </copy>
        
    
    ![Captura de tela do ncview](images/nc22.png)
    

## Tarefa 4: Execução Real e WRF

1.  Nós reduzimos os dados para representar uma localização geográfica e adicionamos dados meteorológicos a ela; seu último tempo para executar WRF usando dados reais.
    
        <copy>
        cd ~/WRF/WRF-4.1.5
        cd run
        rm met_em*
        ln -s ../../WPS-4.1/met_em* .  
        vi namelist.input
        </copy>
        
2.  No arquivo namelist.input, precisamos editar o conteúdo para refletir nossa área geográfica/domínio e nossa data e intervalo.  
    **Altere todos os valores ao seu gosto com base em seu experimento**.
    
    *   `run_days`: Tempo de execução em dias.
    *   `run_hours`: Tempo de execução em horas. **Observação**: se for mais de 1 dia, você poderá usar run\_days e run\_hours ou apenas run\_hours. Por exemplo, se o tamanho total da execução for 36 horas, defina run\_days = 1 e run\_hours = 12 ou run\_days = 0 e run\_hours = 36.
    *   `run_minutes`: Runtime em minutos.
    *   `run_seconds`: Runtime em segundos.
    *   `start_year`: Um ano de 4 dígitos do horário de início.
    *   `start_month`: Um mês de 2 dígitos do horário de início.
    *   `start_day`: Um dia de 2 dígitos do horário de início.
    *   `start_hour`: Uma hora de início com 2 dígitos.
    *   `end_year`: Um ano de 4 dígitos do horário de término.
    *   `end_month`: Um mês de 2 dígitos do horário de término.
    *   `end_day`: Um dia de 2 dígitos da hora de término.
    *   `end_hour`: Uma hora de 2 dígitos da hora de término.
    *   `interval_seconds`: O intervalo de tempo entre os dados reais de entrada, que será o intervalo entre o arquivo de condição do limite lateral.
    *   `e_we`: O índice final na direção x (west\_east) (dimensão alternada).
    *   `e_sn`: O índice final na direção y (norte-sul) (dimensão escalonada).
    *   `e_vert`: índice final na direção z (vertical) (dimensão alternada -- refere-se a níveis completos).
    *   `dx`: O tamanho da grade na direção x (em metros).
    *   `dy`: tamanho da grade na direção y (em metros).
    
        <copy>
        &time_control
        run_days                            = 0,
        run_hours                           = 6,
        run_minutes                         = 0,
        run_seconds                         = 0,
        start_year                          = 2020, 2000, 2000,
        start_month                         = 12,   01,   01,
        start_day                           = 09,   24,   24,
        start_hour                          = 00,   12,   12,
        end_year                            = 2020, 2000, 2000,
        end_month                           = 12,   01,   01,
        end_day                             = 09,   25,   25,
        end_hour                            = 06,   12,   12,
        interval_seconds                    = 10800
        input_from_file                     = .true.,.true.,.true.,
        history_interval                    = 60,  60,   60,
        frames_per_outfile                  = 1, 1000, 1000,                                        
        restart                             = .false.,
        restart_interval                    = 7200,
        io_form_history                     = 2
        io_form_restart                     = 2
        io_form_input                       = 2
        io_form_boundary                    = 2
        /
        
        &domains
        time_step                           = 60,
        time_step_fract_num                 = 0,
        time_step_fract_den                 = 1,
        max_dom                             = 1,
        e_we                                = 100,    112,   94,
        e_sn                                = 100,    97,    91,
        e_vert                              = 40,    33,    33,
        p_top_requested                     = 5000,
        num_metgrid_levels                  = 34,
        num_metgrid_soil_levels             = 4,
        dx                                  = 10000, 10000,  3333.33,
        dy                                  = 10000, 10000,  3333.33,
        grid_id                             = 1,     2,     3,
        parent_id                           = 0,     1,     2,
        i_parent_start                      = 1,     31,    30,
        j_parent_start                      = 1,     17,    30,
        parent_grid_ratio                   = 1,     3,     3,
        parent_time_step_ratio              = 1,     3,     3,
        feedback                            = 1,
        smooth_option                       = 0
        /
        
        &physics
        physics_suite                       = 'CONUS'
        mp_physics                          = -1,    -1,    -1,
        cu_physics                          = -1,    -1,     0,
        ra_lw_physics                       = -1,    -1,    -1,
        ra_sw_physics                       = -1,    -1,    -1,
        bl_pbl_physics                      = -1,    -1,    -1,
        sf_sfclay_physics                   = -1,    -1,    -1,
        sf_surface_physics                  = -1,    -1,    -1,
        radt                                = 30,    30,    30,
        bldt                                = 0,     0,     0,
        cudt                                = 5,     5,     5,
        icloud                              = 1,
        num_land_cat                        = 21,
        sf_urban_physics                    = 0,     0,     0,
        /
        
        &dynamics
        hybrid_opt                          = 2,
        w_damping                           = 1,
        diff_opt                            = 1,      1,      1,
        km_opt                              = 4,      4,      4,
        diff_6th_opt                        = 0,      0,      0,
        diff_6th_factor                     = 0.12,   0.12,   0.12,
        base_temp                           = 290.
        damp_opt                            = 3,
        zdamp                               = 5000.,  5000.,  5000.,
        dampcoef                            = 0.2,    0.2,    0.2
        khdif                               = 0,      0,      0,
        kvdif                               = 0,      0,      0,
        non_hydrostatic                     = .true., .true., .true.,
        moist_adv_opt                       = 1,      1,      1,
        scalar_adv_opt                      = 1,      1,      1,
        gwd_opt                             = 0,
        /
        
        &bdy_control
        spec_bdy_width                      = 5,
        specified                           = .true.
        /
        
        &namelist_quilt
        nio_tasks_per_group = 0,
        nio_groups = 1,
        /
        </copy>
        
    
    **Para sair, basta pressionar esc, em seguida, turno: seguido por wq entrar**
    
3.  Agora, podemos finalmente executar real.exe e wrf.exe. Este programa leva um tempo para ser executado. Para ajudar a acelerar isso, usaremos o MPI para executar os programas em vários núcleos de nossa forma. Lembre-se de que este guia foi escrito usando uma configuração VM.Standard2.16; portanto, executaremos real.exe em 2 núcleos.
    
        <copy>
        cat /proc/cpuinfo #To get cpu infor (use if you forgot your OCI shape).
        mpirun -n 2 ./real.exe #This has real.exe running on two cores
        cat rsl.out.0000 #Use this command check for errors
        </copy>
        
4.  Podemos observar os resultados usando o ncview e observar T2 para ver o valor dos dados baixados.
    
        <copy>
        ncview wrfinput_d01
        </copy>
        
    
    Aqui você pode ver a temperatura em dois metros para a área de destino. ![Captura de tela do ncview](images/nc23.png)
    
5.  Temos nossa entrada, agora vamos usá-la para gerar uma previsão. Executaremos real.exe usando 10 núcleos.
    
        <copy>
        mpirun -n 10 ./wrf.exe #This will run on 10 cores
        tail -F rsl.out.0000 #can be used to check for errors and progress.
        </copy>
        
6.  Podemos observar os resultados de nossa previsão com o ncview, com o seguinte:
    
        <copy>
        ncview wrfout_d01_2020-11-20_06\:00\:00 
        </copy>
        
    
    ![Captura de tela do ncview](images/nc24.png)  
    Da nossa previsão você pode ver que algumas mudanças ocorreram quando olhamos para a mesma área seis horas depois. Há muitas variáveis para explorar, então se divirta.
    

## Confirmação

*   **Autor** - Brian Bennett, Engenheiro de Soluções, Big Compute
*   **Última Atualização por/Data** - Brian Bennett, Big Compute, dezembro de 2020