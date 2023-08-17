# 在 HPC 集群上设置、安装和运行 WRF

## 简介

此实验室将讨论并引导您了解如何在 OCI 基础结构上设置和运行 WRF 集群。本指南旨在向您展示如何创建两个节点 HPC 集群，以设置能够在其中运行实验和天气模拟的环境。

估计实验室时间：90 分钟

### 目标

在本练习中，您将了解：

*   部署两个节点 HPC 集群
*   下载相关项和 WRF/WPS
*   为 WRF/WPS 编译库
*   编译 WRF 和 WPS
*   使用 Geogrid 创建 WRF 域
*   下载 GFS 数据并运行 Ungrib 和 Metgrid
*   使用真实数据运行 WRF

### 前提条件

本练习假定您具有：

*   Oracle 试用或付费云账户
*   请注意，在此实验中，我们将为集群使用两个 **BM.HPC2.36** 节点，为堡垒使用一个 VM.Standard2.4。

## 任务 1：设置 HPC 基础结构

1.  使用左侧的导航菜单并导航到**解决方案和平台** > 市场 > 应用产品。
2.  在搜索栏中键入 HPC，然后按 ENTER/Return 键。
3.  在“All Applications（所有应用程序）”部分中，选择 HPC 集群，然后启动堆栈。它应当从左侧查看图像，后跟右侧的图像。  
    ![HPC 映像的屏幕快照](images/hpc2.png)
4.  在“创建堆栈”菜单中，根据需要提供名称和说明，然后单击“下一步”。
5.  选择一个 AD，粘贴到 SSH 密钥并保持 "CONFIGURE NFS SHARE"（配置 NFS 共享）框处于选中状态。
6.  对于堡垒，选择与集群相同的 AD 并选择 **VM.Standard.2.4** 配置，然后单击“下一步”。
7.  在审核屏幕上，仔细检查您输入的所有信息，然后单击“Create（创建）”。

## 任务 2：设置 Gnome 桌面

1.  您可以使用许多不同的桌面技术，但本指南将介绍如何设置和配置 Gnome 桌面，因为它是 Oracle Linux 的默认桌面。以下步骤将下载、安装和配置 Oracle Linux 7 堡垒实例的 Gnome 桌面。
    
        <copy>
        sudo yum groupinstall "server with gui"
        Y
        sudo yum install tigervnc-server
        Y
        vncserver # DO NOT MAKE READ ONLY and password should be no greater than 8 characters
        </copy>
        
2.  打开本地终端  
    使用与实例相关的信息运行以下命令：ssh -L 5901:localhost:5901 opc@IPADDRESS ![SSH 屏幕快照](images/ssh.png)
    
3.  在“VNC 服务器：”部分中打开 TigerVNC 查看器类型 `localhost:1`  
    然后单击“连接”  
    ![TigerVNC 查看器的屏幕截图](images/tigervnc.png)
    
4.  输入 VNC 密码  
    ，然后单击“确定”  
    。 ![VNC 验证的屏幕截图](images/vncauth.png)
    
5.  您最终会收到一个美好的桌面，您应该继续从这里开始工作。  
    ![Gnome 桌面屏幕截图](images/gnomedesktop.png)
    

## 任务 3：配置 Oracle Linux

1.  现在，我们可以访问实例的桌面环境，开始对其进行配置以运行 WRF。要开始安装依赖项，我们需要前进。单击左上角的 "Applications"（应用程序），单击 "System Tools"（系统工具），然后单击 "Terminal"（终端）。在终端中执行以下操作。
    
        <copy>
        sudo yum update -y
        sudo yum upgrade -y
        sudo yum group install "Development Tools" -y
        sudo yum install ncview -y
        sudo yum install mc -y
        </copy>
        

## 任务 4：下载和编译 WRF 的库

现在我们已经安装了大多数依赖项，我们将需要 WRF，让我们开始编译 WRF 所需的库。

### 创建文件夹结构和下载库

1.  在远程终端中输入以下命令以开始设置所需的文件夹结构。
    
        <copy>  
        cd /mnt/nfs-share
        mkdir WRF
        cd WRF
        mkdir downloads
        cd downloads
        </copy>
        
2.  在 downloads 目录中，可以使用以下命令下载我们需要的所有库。
    
        <copy>
        curl -O ftp://ftp.unidata.ucar.edu/pub/netcdf/old/netcdf-4.1.2.tar.gz  
        curl -O https://www.zlib.net/zlib-1.2.11.tar.gz  
        curl -O ftp://ftp.simplesystems.org/pub/libpng/png/src/libpng16/libpng-1.6.37.tar.gz  
        curl -O https://www2.mmm.ucar.edu/wrf/OnLineTutorial/compile_tutorial/tar_files/jasper-1.900.1.tar.gz  
        curl -O https://www.mpich.org/static/downloads/3.3.2/mpich-3.3.2.tar.gz
        </copy>
        
3.  不要逐个解压缩文件，而是使用 **loop** 为我们执行此操作，然后返回到 WRF 目录。
    
        <copy>
        for i in *.gz ; do tar -xzf $i ; done
        cd /mnt/nfs-share/WRF  
        </copy>
        
4.  现在我们回到 WRF 目录中，让我们为 WRF 设置库的文件夹结构。WE 还将创建一个环境变量来引用本指南后面要使用的库路径。
    
        <copy>
        mkdir libs  
        cd libs  
        mkdir netcdf  
        mkdir mpich  
        mkdir grib2 # (this will be used for the jasper, libpng, and zlib libraries)  
        export LIBDIR=/mnt/nfs-share/WRF/libs
        </copy>
        

### 编译 grib2 库

grib2 库实际上是三个独立库（特别是 zlib、jasper 和 libpng）的编译。

5.  编译 Zlib
    
        <copy> 
        cd /mnt/nfs-share/WRF/downloads
        cd zlib-1.2.11
        ./configure --prefix=$LIBDIR/grib2
        make
        make install
        </copy>
        
6.  正在编译 libpng
    
        <copy>
        cd /mnt/nfs-share/WRF/downloads
        cd libpng-1.6.37/
        ./configure --prefix=$LIBDIR/grib2 LDFLAGS="-L$LIBDIR/grib2/lib" CPPFLAGS="-I$LIBDIR/grib2/include"
        make
        make install
        </copy>
        
7.  编译 jasper
    
        <copy>
        cd /mnt/nfs-share/WRF/downloads
        cd jasper-1.900.1/
        ./configure --prefix=$LIBDIR/grib2
        make
        make install
        </copy>
        

### 编译 Netcdf 和 Mpich 库

8.  编译 netcdf 库
    
        <copy>
        cd /mnt/nfs-share/WRF/downloads
        cd netcdf-4.1.2/
        ./configure --prefix=$LIBDIR/netcdf --disable-dap --disable-netcdf-4
        make
        make install
        </copy>
        
9.  编译 mpich 库
    
        <copy>
        cd /mnt/nfs-share/WRF/downloads
        cd mpich-3.3.2/
        ./configure --prefix=$LIBDIR/mpich
        make
        make install
        </copy>
        

## 任务 5：编译 WRF 和 WPS

1.  现在我们已经为库设置了文件夹结构，我们可以开始下载和编译 WRF 和 WPS。以下代码块将下载程序并将其放置在相应的位置。
    
        <copy>
        cd /mnt/nfs-share/WRF/downloads
        wget https://github.com/wrf-model/WRF/archive/v4.1.5.tar.gz
        wget https://github.com/wrf-model/WPS/archive/v4.1.tar.gz
        for i in *.gz ; do tar -xzf $i ; done
        mv WRF-4.1.5/ /mnt/nfs-share/WRF/
        mv WPS-4.1/ /mnt/nfs-share/WRF/
        </copy>
        
2.  在编译 WRF 之前，我们需要设置一些环境变量，以便程序能够找到并使用已编译为正常运行的库。
    
        <copy>
        cd .. or /mnt/nfs-share/WRF
        cd WRF-4.1.5/
        export NETCDF=$LIBDIR/netcdf
        export PATH=$LIBDIR/mpich/bin:$PATH
        export JASPERLIB=$LIBDIR/grib2/lib
        export JASPERINC=$LIBDIR/grib2/include
        </copy>
        
3.  现在我们已经将所有库引用到环境变量，我们终于可以编译 WRF。对于该实验室，我们将编译 WRF，以便能够使用已收集的真实气象数据。我们将它分成两部分：配置和编译部分。
    
    **配置：**  
    我们选择选项 34 来继续选择使用 gfortran/gcc 和选项 1，因为本指南中将不包含嵌套内容。
    
        <copy>  
        ./configure
        34 (dmpar)
        1
        vi ~/.bashrc  
            export LIBDIR=/mnt/nfs-share/WRF/libs  
            export LD_LIBRARY_PATH=$LIBDIR/netcdf/lib:$LIBDIR/grib2/lib   
            export PATH=$LIBDIR/mpich/bin:$PATH
        source ~/.bashrc
        </copy>
        
    
    **编译：**  
    在此处，我们编译 WRF 并测试它将运行。
    
        <copy>
        ./compile em_real
        cd main
        ./wrf.exe
        ./real.exe
        </copy>
        
4.  现在 WRF 已编译，我们需要编译 WPS。必须先编译 WRF。在此，我们选择选项 3，因为我们使用的是 x86 基础结构 (OCI BM.HPC2.36) 上的 Linux 操作系统 (Oracle Linux) 以及 gfortran 编译器。
    
        <copy>
        cd /mnt/nfs-share/WRF/WPS-4.1
        export WRF_DIR=/mnt/nfs-share/WRF/WRF-4.1.5/
        ./configure
        3
        ./compile
        </copy>
        
5.  现在 WPS 已编译，我们需要下载用于模拟世界地理的数据。我们将创建一个新文件夹来存储此信息。由于我们使用推荐的高分辨率文件，因此需要一段时间才能解压缩，因此我们使用 PV 对话框生成一个进度条。
    
        <copy>
        cd /mnt/nfs-share/WRF/
        mkdir GEOG
        cd GEOG
        wget https://www2.mmm.ucar.edu/wrf/src/wps_files/geog_high_res_mandatory.tar.gz  
        tar xzf geog_high_res_mandatory.tar.gz
        </copy>
        
6.  现在，我们已经下载并提取了地理位置数据，我们需要将一些文件移动到正确的位置，并删除一些不需要的其他文件。为此，我们将使用午夜指挥官，但您可以根据需要使用终端命令。mc 之后的以下内容将使用 Midnight Commander 接口，因此 **`DO NOT`** `copy the following entire code block into terminal`。
    
        <copy>
        mc    #Opens Midnight Commander
        Click on the left WPS_GEOG
        Use CTRL + T to highlight all the folders
        F6    #This will move the files to the GEOG directory
        ENTER #This will confirm the choice
        F10   #This is used to exit Midnight commander
        sudo rm -r WPS_GEOG   #This will delete the additional GEOG folder.
        mc    #Opens Midnight Commander
        Highlight *._WPS_GEOG
        F8    #This deletes the highlighted file
        ENTER #This will confirm the choice
        F10 #This is used to exit Midnight commander
        </copy>
        
7.  我们需要在所选位置将 namelist.wps 文件调整为零。我将是一个小电网（10,000 x 10,000 米），Woburn Massachusetts USA 作为中心点。
    
        <copy>
        cd /mnt/nfs-share/WRF/WPS-4.1
        vi namelist.wps
        </copy>
        
    
    **请根据您的实验将下面的所有值更改为您的喜欢值**。
    
    *   `max_dom`：用于指定模拟中域总数（包括父域）的整数。默认值为 1。
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
            ref_lat   =  42.48,  
            ref_lon   = -71.15,  
            truelat1  =  42.48,  
            truelat2  =  42.48,  
            stand_lon = -71.15,  
            geog_data_path = '/mnt/nfs-share/WRF/GEOG/'  
            / 
        </copy>     
        
    
    **要退出，只需按 Esc 键，然后按 shift：后跟 wq enter**  
    **所有这些更改都适用于以 Woburn MA 作为震中器的地理区域。**
    
8.  现在我们已经输入了我们感兴趣的地理区域的信息（本指南使用 Woburn MA USA 作为中心点）让我们使用 ncview 在运行 Geogrid 程序后验证我们有正确的位置。
    
        <copy>
        ./geogrid.exe
        sudo ncview geo_em.d01.nc
        </copy>
        
    
    使用 2d var 检查着陆掩码以验证位置。如果对图像满意，我们将创建域。
    
    ![ncview 的屏幕截图](images/nc1.png)
    
9.  设置地理位置区域或域后。我们现在需要获得气象数据以覆盖域。
    
    1.  在 Web 浏览器中，导航到 https://nomads.ncep.noaa.gov/pub/data/nccf/com/gfs/prod/
    2.  单击包含最新日期的链接（例如 gfs.20210120/，今天是 2021 年 1 月 20 日）
    3.  选择 00  
        不要实际下载任何内容。我们将为此创建一个脚本。我们将采用较低的分辨率，以便更轻松地处理此实验室的数据大小，例如 0p50 而不是 0p25。如果您有额外的存储并且需要更高的分辨率/更可靠的数据，请使用 0p25。需要在要运行的时间内下载文件数。每个文件都是指定间隔的一小时数据。EX（如果要运行 6 个小时，则需要 gfs.t00z.pgrb2.0p50.f000、gfs.t00z.pgrb2.0p50.f003 和 gfs.t00z.pgrb2.0p50.f006。0p25 只需 1 小时的步骤，0p50 只需 3 小时的步骤。）对于教程，我们只使用价值 6 小时的数据。随后的 WRF 使用变得更加舒适，随意使用更多。
10.  让我们导航到正确的目录并创建脚本来下载数据。
    
        <copy>
        cd /mnt/nfs-share/WRF
        mkdir scripts
        mkdir GFS
        cd scripts
        vi download_gfs.sh
        </copy>
        
11.  脚本如下：
    
        <copy>
        #!/bin/bash  
        
        inputdir=/mnt/nfs-share/WRF/GFS  
        rm -rf $inputdir  
        mkdir $inputdir  
        
        year=2021  
        month=01  
        day=20  
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
    **此脚本将在 0p50 解析中下载日期 01/20/21 的 SIX 小时数据。请根据您的需求进行调整。**
    
12.  以下命令使脚本可执行并运行它来下载数据：
    
        <copy>
        chmod +x download_gfs.sh
        ./download_gfs.sh
        </copy>
        
13.  现在我们已经下载了数据，让我们继续完成在域上覆盖数据的过程。我们将使用 ungrib 和 Metgrid 来完成此任务。我们将它分成两个部分：配置和运行部分。
    
    **配置：**
    
        <copy>
        cd /mnt/nfs-share/WRF/WPS-4.1
        ln -s ungrib/Variable_Tables/Vtable.GFS ./Vtable
        ./link_grib.csh /mnt/nfs-share/WRF/GFS/
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
        start_date = '2021-01-20_00:00:00',         # start time  
        end_date   = '2021-01-20_06:00:00',         #6 hours later than start time  
        interval_seconds = 10800                    #3 hours worth of seconds interval between steps  
        io_form_geogrid = 2,  
        / 
        </copy>
        
    
    **要退出，只需按 Esc，然后按 shift：后跟 wq enter**
    
    **运行：**
    
        <copy>
        ./ungrib.exe
        ln -s metgrid/METGRID.TBL.ARW ./METGRID.TBL
        ./metgrid.exe
        </copy>
        
14.  我们可以通过使用 ncview 并查看 skintemp 来查看结果，以了解我们下载的数据的值。
    
        <copy>
        sudo ncview met_em.d01.2021-01-20_00:00:00.nc 
        </copy>
        
    
    ![ncview 的屏幕截图](images/nc2.png)
    

## 任务 6：运行实际和 WRF

1.  我们有下溢的数据来表示地理位置，我们已经向它添加了气象数据；它最后一次使用真实数据运行 WRF。
    
        <copy>
        cd /mnt/nfs-share/WRF/WRF-4.1.5
        cd run
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
    *   `frames_per_outfile`：拆分输出文件的频率。
    *   `num_metgrid_levels`：WPS 输出中的垂直级别数。
        *   在 met\_em\* 文件之一上键入 ncdump -h 以查找此数字。
    *   `w_damping`：垂直速度阻塞标志（供操作使用）。设置为 1。
    
        <copy>
        &time_control
        run_days                            = 0,
        run_hours                           = 6,
        run_minutes                         = 0,
        run_seconds                         = 0,
        start_year                          = 2021, 2000, 2000,
        start_month                         = 01,   01,   01,
        start_day                           = 20,   24,   24,
        start_hour                          = 00,   12,   12,
        end_year                            = 2021, 2000, 2000,
        end_month                           = 01,   01,   01,
        end_day                             = 20,   25,   25,
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
    
3.  现在，我们终于可以运行 real.exe 和 wrf.exe。此程序需要一些时间才能运行。为了加快此速度，我们将使用 MPI 在 HPC 集群的多个核心和节点上运行程序。请记住，本指南是使用用于堡垒服务器的 VM.Standard2.4 配置编写的，因此我们将在 2 个核心上运行 real.exe。
    
        <copy>
        cat /proc/cpuinfo #To get cpu infor (use if you forgot your OCI shape).
        mpirun -n 2 ./real.exe #This has real.exe running on two cores
        cat rsl.out.0000 #Use this command check for errors
        </copy>
        
4.  让我们使用 ncview 来查看我们的输入文件。
    
        <copy>
        sudo ncview wrfinput_d01
        </copy>
        
    
    在这里，您可以看到目标区域的温度为两米。 ![ncview 的屏幕截图](images/nc3.png)
    
5.  我们有我们的输入，现在可以用它来生成预测。
    
        <copy>
        mpirun -np 72 -hosts 172.16.1.2,172.16.1.3 ./wrf.exe #This will run on 72 cores on two nodes with ipaddesses of 172.16.1.2 and 172.16.1.3
        tail -F rsl.out.0000 #can be used to check for errors and progress. 
        </copy>
        
6.  我们可以通过以下方式检查预测：
    
        <copy>
        sudo ncview wrfout_d01_2020-11-20_06\:00\:00 
        </copy>
        
    
    ![ncview 的屏幕截图](images/nc4.png)  
    从我们的预测可以看出，六小时后我们看到同一区域发生了一些变化。有很多变量可供探索，所以玩得开心。
    
    借此，您已了解如何设置和配置 WRF 以使用 HPC 集群上的实际数据运行天气预测。有很多可探索的变量和配置，所以很有趣。
    

## 确认

*   **作者** - Big Compute 解决方案工程师 Brian Bennett
*   **上次更新者/日期** - Brian Bennett，大计算，2021 年 1 月