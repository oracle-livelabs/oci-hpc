# 使用 WRF 更改实验数据

## 简介

这是与“WRF 设置、安装和运行指南”比较的较小指南。在完成“WRF 设置、安装和运行指南”或者使用下载链接开始使用已经设置了 WRF 4.1.5 和 WPS 4.1 的定制映像后，将使用此指南。

估计实验室时间：45 分钟

### 目标

在本练习中，您将了解：

*   使用 Geogrid 创建 WRF 域
*   下载 GFS 数据并运行 Ungrib 和 Metgrid
*   使用真实数据运行 WRF

### 前提条件

本练习假定您具有：

*   Oracle 免费层级或付费云账户
*   访问实验室 1 和 2 中涵盖的 Ubuntu 18。04 CPU 实例
    *   请注意，本实验使用的是 **VM.Standard2.16 配置**。如果需要，可以使用较小的形状。
*   已通过“WRF 设置、安装和运行指南”或[此处](https://objectstorage.us-ashburn-1.oraclecloud.com/p/lRqMYYN5VTdSgBz9f8nv7Tz5mzMGMqr7wlN2Y_q6g6GHmTdc9GX8lokgmTui81BA/n/hpc_limited_availability/b/Demo_Materials/o/WRF_DEMOV2)下载了自定义映像。
    *   **注**：Ubuntu 口令为 ppkkego，vnc 口令为 ppkkego  
        可以在远程终端中使用以下命令进行更改：
        
            <copy>
            sudo passwd ubuntu
            sudo vncpasswd
            </copy> 
            

## 任务 1：连接到 Gnome 桌面

我们将使用 [TigerVNC Viewer](https://tigervnc.org/) 连接到实例。

1.  打开本地终端
    
    使用与实例相关的信息运行以下命令：ssh -L 5901:localhost:5901 ubuntu@IPADDRESS ![ssh 的屏幕截图](images/ssh.png)
    
2.  启动 vncserver 会话  
    vncserver  
    ![ssh 的屏幕截图](images/vncstart.png)
    
3.  打开 TigerVNC 查看器
    
    VNC 服务器：localhost:1  
    然后单击“连接”  
    ![TigerVNC 查看器的屏幕截图](images/tigervnc.png)
    
4.  输入 VNC 密码  
    ，然后单击“确定”  
    。 ![VNC 验证的屏幕截图](images/vncauth.png)
    
5.  您最终会收到一个美好的桌面，您应该继续从这里开始工作。  
    ![Gnome 桌面屏幕截图](images/gnomedesktop.png)
    

## 任务 2：创建地理区域/域

1.  我们需要在所选位置将 namelist.wps 文件调整为零。它将是一个小型电网（10,000 x 10,000 米），休斯顿德克萨斯州作为中心点。
    
        <copy>
        cd ~/WRF/WPS-4.1
        vi namelist.wps
        </copy>
        
    
    **请根据您的实验将下面的所有值更改为您的喜欢值**。
    
    *   `max_dom` 一个整数，用于指定模拟中域的总数，包括父域。默认值为 1。
    *   `e_we`：用于指定网格中西部完整维的整数。无默认值。
    *   `e_sn`：指定网格中南北完整维度的整数。无默认值。
    *   `dx`：实际值，用于指定映射比例系数为 1 的 x 方向中的网格距离（以米为单位）。
    *   `dy`：实际值，用于指定映射比例系数为 1 的 y 方向中的网格距离（以米为单位）。
    *   `ref_lat`：一个实际值，指定模拟域中 (i，j) 位置的（纬度，经度）位置的纬度部分。
    *   `ref_lon`：一个实际值，指定模拟域中 (i，j) 位置的（纬度，经度）位置的经度部分。
    *   `truelat1`：一个实际值，用于指定 Lambert 符合性映射的第一个真实纬度，或者指定 Mercator 和极坐标定图预测的唯一真实纬度。
    *   `truelat2`：一个实际值，用于指定 Lambert 符合性方案预测的第二个真实纬度。
    *   `stand_lon`：一个实际值，用于指定与 Lambert 符合和极地立体图预测中的 y 轴并行的经度。对于常规纬度经度预测，此值提供有关地球地理极点的旋转。
    *   `geog_data_path`：表示可找到地理数据目录的目录的相对路径或绝对路径的字符串。
    
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
        
    
    **要退出，只需按 Esc 键，然后按 shift：后跟 wq enter**  
    **所有这些更改都适用于以 Houston TX 为中心位置的地理区域。**
    
2.  现在我们已经输入了我们感兴趣的地理区域的信息（本指南使用休斯顿 TX USA 作为中心点）让我们使用 ncview 在运行 Geogrid 程序后验证我们有正确的位置。
    
        <copy>
        ./geogrid.exe
        ncview geo_em.d01.nc
        </copy>
        
3.  使用 2d var 检查着陆掩码以验证位置。如果对图像满意，我们将创建域。
    
    ![ncview 的屏幕截图](images/nc21.png)
    

## 任务 3：向地理区域/域添加气象数据

设置地理位置区域或域后。我们现在需要获得气象数据以覆盖域。

1.  在 Web 浏览器中，导航到 https://nomads.ncep.noaa.gov/pub/data/nccf/com/gfs/prod/
    
2.  单击包含最新日期的链接（例如 gfs.20201120/，今天是 2020 年 11 月 20 日）
    
3.  选择 00  
    `Don’t actually download anything.` 我们将为此创建脚本。我们将采用较低的分辨率，以便更轻松地处理此实验室的数据大小，例如 0p50 而不是 0p25。如果您有额外的存储并且需要更高的分辨率/更可靠的数据，请使用 0p25。需要在要运行的时间内下载文件数。每个文件都是指定间隔的一小时数据。EX（如果要运行 6 个小时，则需要 gfs.t00z.pgrb2.0p50.f000、gfs.t00z.pgrb2.0p50.f003 和 gfs.t00z.pgrb2.0p50.f006。0p25 只需 1 小时的步骤，0p50 只需 3 小时的步骤。）对于教程，我们只使用价值 6 小时的数据。随后的 WRF 使用变得更加舒适，随意使用更多。
    
4.  让我们导航到正确的目录并编辑预创建的脚本来下载数据。
    
        <copy>
        cd ~/WRF
        cd scripts
        vi download_gfs.sh
        </copy>
        
    
    脚本将为以下内容：  
    您只需编辑要下载的数据的 `year`、`month` 和 `day`。
    
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
        
    
    **要退出，只需按 Esc 键，然后按 shift：后跟 wq enter**  
    **此脚本将通过 0p50 解析下载日期 12/09/20 的 SIX 小时数据。请根据您的需求进行调整。**
    
5.  以下命令使脚本可执行并运行它来下载数据：
    
        <copy>
        chmod +x download_gfs.sh
        ./download_gfs.sh
        </copy>
        
6.  现在我们已经下载了数据，让我们继续完成在域上覆盖数据的过程。我们将使用 ungrib 和 Metgrid 来完成此任务。我们将它分成两个部分：配置和运行部分。
    
    **配置：**
    
        <copy>
        cd ~/WRF/WPS-4.1
        rm ./Vtable
        ln -s ungrib/Variable_Tables/Vtable.GFS ./Vtable
        ./link_grib.csh ~/WRF/GFS/
        vi namelist.wps 
        </copy> 
        
    
    **请根据您下载的文件更改以下所有值**。
    
    *   `start_date` 的格式为：Year-Month-Day\_hour:minute:second
    *   `end_date` 的格式为：Year-Month-Day\_hour:minute:second
    *   `interval seconds`：时间变化的气象输入文件之间的整数秒数。无默认值。
    
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
        
    
    **要退出，只需按 Esc，然后按 shift：后跟 wq enter**
    
    **运行：**
    
        <copy>
        ./ungrib.exe
        rm ./METGRID.TBL 
        ln -s metgrid/METGRID.TBL.ARW ./METGRID.TBL
        ./metgrid.exe
        </copy>
        
7.  我们可以通过使用 ncview 并查看 skintemp 来查看所下载数据的值。
    
        <copy>
        ncview met_em.d01.2020-12-09_00\:00\:00.nc 
        </copy>
        
    
    ![ncview 的屏幕截图](images/nc22.png)
    

## 任务 4：运行实际和 WRF

1.  我们有下溢的数据来表示地理位置，我们已经向它添加了气象数据；它最后一次使用真实数据运行 WRF。
    
        <copy>
        cd ~/WRF/WRF-4.1.5
        cd run
        rm met_em*
        ln -s ../../WPS-4.1/met_em* .  
        vi namelist.input
        </copy>
        
2.  在 namelist.input 文件中，我们需要编辑内容以反映地理区域/域以及日期和间隔。  
    **请根据实验将所有值更改为喜欢的值**。
    
    *   `run_days`：以天为单位的运行时间。
    *   `run_hours`：以小时为单位的运行时间。**注**：如果超过 1 天，您可以同时使用 run\_days 和 run\_hours 或仅使用 run\_hours。例如，如果总运行长度为 36 小时，则可以设置 run\_days = 1，run\_hours = 12，或者 run\_days = 0，run\_hours = 36。
    *   `run_minutes`：以分钟为单位的运行时间。
    *   `run_seconds`：以秒为单位的运行时间。
    *   `start_year`：开始时间的 4 位数年份。
    *   `start_month`：开始时间的 2 位数月份。
    *   `start_day`：开始时间的 2 位数。
    *   `start_hour`：开始时间的两位数小时。
    *   `end_year`：结束时间的 4 位数年份。
    *   `end_month`：结束时间的 2 位数月份。
    *   `end_day`：结束时间的两位数日。
    *   `end_hour`：结束时间的两位数小时。
    *   `interval_seconds`：传入实际数据之间的时间间隔，即横向边界条件文件之间的间隔。
    *   `e_we`：以 x (west\_east) 方向（刻度维）表示的结束索引。
    *   `e_sn`：y（南北）方向的结束索引（水平维）。
    *   `e_vert`：z（垂直）方向的结束索引（水平维 - 这指的是完整级别）。
    *   `dx`：以 x 方向表示的网格长度（以米为单位）。
    *   `dy`：以 y 方向表示的网格长度（以米为单位）。
    
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
        
    
    **要退出，只需按 Esc，然后按 shift：后跟 wq enter**
    
3.  现在，我们终于可以运行 real.exe 和 wrf.exe。此程序需要一些时间才能运行。为了加快这一速度，我们将使用 MPI 在形状的多个核心上运行程序。请记住，本指南是使用 VM.Standard2.16 配置编写的，因此我们将在 2 个核心上运行 real.exe。
    
        <copy>
        cat /proc/cpuinfo #To get cpu infor (use if you forgot your OCI shape).
        mpirun -n 2 ./real.exe #This has real.exe running on two cores
        cat rsl.out.0000 #Use this command check for errors
        </copy>
        
4.  我们可以通过使用 ncview 并查看 T2 来查看所下载数据的值来查看结果。
    
        <copy>
        ncview wrfinput_d01
        </copy>
        
    
    在这里，您可以看到目标区域的温度为两米。 ![ncview 的屏幕截图](images/nc23.png)
    
5.  我们有我们的输入，现在可以用它来生成预测。我们将使用 10 个核心运行 real.exe。
    
        <copy>
        mpirun -n 10 ./wrf.exe #This will run on 10 cores
        tail -F rsl.out.0000 #can be used to check for errors and progress.
        </copy>
        
6.  我们可以用 ncview 查看预测的结果，如下所示：
    
        <copy>
        ncview wrfout_d01_2020-11-20_06\:00\:00 
        </copy>
        
    
    ![ncview 的屏幕截图](images/nc24.png)  
    从我们的预测可以看出，六小时后我们看到同一区域发生了一些变化。有很多变量可供探索，所以玩得开心。
    

## 确认

*   **作者** - Big Compute 解决方案工程师 Brian Bennett
*   **上次更新者/日期** - Brian Bennett，大计算，2020 年 12 月