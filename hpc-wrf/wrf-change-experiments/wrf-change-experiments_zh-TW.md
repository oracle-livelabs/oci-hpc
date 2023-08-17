# 使用 WRF 變更實驗資料

## 簡介

與「WRF 設定」、「安裝」和「執行指南」相比，這是較小指南。本指南適用於您完成 WRF 安裝、安裝和執行指南後，或是使用下載連結開始使用自訂影像 (已設定 WRF 4.1.5 和 WPS 4.1)。

預估實驗室時間：45 分鐘

### 目標

在此實驗室中，您將瞭解：

*   使用 Geogrid 建立 WRF 網域
*   下載 GFS 資料並執行 Ungrib 和 Metgrid
*   以真實資料執行 WRF

### 必要條件

此實驗室假設您具有：

*   Oracle Free Tier 或 Paid Cloud 帳戶
*   存取 Ubuntu 18。04 CPU 執行處理，如實驗室 1 和 2 中所涵蓋
    *   請注意，我們將使用 **VM.Standard2.16 資源配置**在這個實驗室中。如果需要，您可以使用較小的形狀。
*   已通過 WRF 安裝、安裝和執行指南，或者從[此處](https://objectstorage.us-ashburn-1.oraclecloud.com/p/lRqMYYN5VTdSgBz9f8nv7Tz5mzMGMqr7wlN2Y_q6g6GHmTdc9GX8lokgmTui81BA/n/hpc_limited_availability/b/Demo_Materials/o/WRF_DEMOV2)下載自訂影像。
    *   **注意**：Ubuntu 密碼是 ppkkego，vnc 密碼是 ppkkego  
        ，您可以使用遠端終端機中的下列命令來變更密碼：
        
            <copy>
            sudo passwd ubuntu
            sudo vncpasswd
            </copy> 
            

## 作業 1：連線至 Gnome 桌面

我們將使用 [TigerVNC 檢視器](https://tigervnc.org/)連線至我們的執行處理。

1.  開啟本機終端機
    
    使用與您的執行處理有關的資訊執行下列命令：ssh -L 5901:localhost:5901 ubuntu@IPADDRESS ![ssh 的畫面擷圖](images/ssh.png)
    
2.  啟動 vncserver 階段作業  
    vncserver  
    ![ssh 的畫面擷圖](images/vncstart.png)
    
3.  開啟 TigerVNC 檢視器
    
    VNC 伺服器：localhost:1  
    然後按一下連線  
    ![TigerVNC 檢視器的螢幕擷取畫面](images/tigervnc.png)
    
4.  輸入您的 VNC 密碼  
    ，然後按一下「確定」  
    ![VNC 認證螢幕快照](images/vncauth.png)
    
5.  您將會在最後獲得一個 GNOME 桌面，並繼續在此工作。  
    ![Gnome 桌面的螢幕快照](images/gnomedesktop.png)
    

## 作業 2：建立地理區域 / 網域

1.  我們必須在您選擇的位置將 namelist.wps 檔案調整為零。這將會是美國休士頓 (Houston Texas USA) 的小方格 (10,000 x 10,000 公尺)，作為中心點。
    
        <copy>
        cd ~/WRF/WPS-4.1
        vi namelist.wps
        </copy>
        
    
    **請依據您的實驗，將下方所有的值變更為按讚**。
    
    *   `max_dom` 指定模擬中網域總數 (包括父項網域) 的整數。預設值為 1。
    *   `e_we`：指定方格中完整西方維度的整數。沒有預設值。
    *   `e_sn`：指定方格中完整南北維度的整數。沒有預設值。
    *   `dx`：在地圖比例係數為 1 的 X 方向中指定網格距離 (公尺) 的實際值。
    *   `dy`：指定地圖比例係數為 1 之 Y 方向的格線距離 (公尺) 的實際值。
    *   `ref_lat`：表示 (i，j) 位置在模擬網域中已知 (latitude，longitude) 位置的緯度部份的真實值。
    *   `ref_lon`：表示 (i，j) 位置在模擬網域中已知 (latitude，longitude) 位置的經度部分的真實值。
    *   `truelat1`：指定 Lambert 符合投影第一個真實緯度的實數值，或 Mercator 和極地立體投影的唯一真實緯度。
    *   `truelat2`：為 Lambert 符合的圓錐投影指定第二個真實緯度的實數值。
    *   `stand_lon`：指定與 Lambert 中 y 軸平行之經度與極立體投影的真實數值。對於正規的經緯度投影，此值會提供地球地理極度的旋轉。
    *   `geog_data_path`：提供可找到地理資料目錄之目錄相對或絕對路徑的字元字串。
    
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
        
    
    **若要結束，只要按 Esc 鍵，然後按 shift:wq Enter 鍵**  
    **這些變更全部適用於使用 Houston TX 做為 epicenter 的地理區域。**
    
2.  現在我們把資訊放在我們感興趣的地理區域 (本指南使用美國休士頓作為中心點) 以使用 ncview 在執行地理網格程式之後，驗證我們有正確的位置。
    
        <copy>
        ./geogrid.exe
        ncview geo_em.d01.nc
        </copy>
        
3.  使用 2D 變體檢查地形遮罩以驗證位置。如果您對映像檔感到滿意，我們就會完成建立網域。
    
    ![ncview 的快照](images/nc21.png)
    

## 任務 3：新增氣象資料至地理區域 / 網域

設定我們的地理區域或網域後。我們現在需要取得氣象資料，才能覆蓋到網域。

1.  在 Web 瀏覽器中，瀏覽至 https://nomads.ncep.noaa.gov/pub/data/nccf/com/gfs/prod/
    
2.  按一下最新日期的連結 (例如 gfs.20201120/，因為今天是 2020 年 11 月 20 日)
    
3.  選擇 00  
    `Don’t actually download anything.` 我們將會建立該指令碼。我們將採用較低的解析度，以便更容易處理此實驗室的資料大小，例如 0p50 (而非 0p25)。如果您有額外的儲存，並且想要更高解析度 / 更可靠的資料，請使用 0p25。需要下載檔案數的時間長度 。每個檔案都是所指定間隔的一小時資料。如果您要執行 6 小時，您需要 gfs.t00z.pgrb2.0p50.f000、gfs.t00z.pgrb2.0p50.f003 和 gfs.t00z.pgrb2.0p50.f006。0p25 的步驟為 1 小時，而 0p50 則為 3 小時。我們只會在教學課程中使用 6 小時的資料。稍後使用 WRF 時，您可以隨時隨地使用更多功能。
    
4.  瀏覽至正確的目錄，並編輯預先建立的命令檔以下載資料。
    
        <copy>
        cd ~/WRF
        cd scripts
        vi download_gfs.sh
        </copy>
        
    
    指令碼如下：  
    您只需要編輯想要下載之資料的 `year`、`month` 及 `day`。
    
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
        
    
    **若要結束，只要按 Esc 鍵，然後按 shift:wq Enter 鍵**  
    **此指令碼就會下載日期 12/09/20 的 SIX 小時資料，網址為 0p50。請調整以符合您的需求。**
    
5.  下列指令可讓指令碼執行，並加以執行以下載資料：
    
        <copy>
        chmod +x download_gfs.sh
        ./download_gfs.sh
        </copy>
        
6.  已經下載好資料，可以透過我們的網域蓋過資料。我們將使用不賄賂和祝福來達成這一點。我們將分成兩個區段：設定和執行區段。
    
    **設定：**
    
        <copy>
        cd ~/WRF/WPS-4.1
        rm ./Vtable
        ln -s ungrib/Variable_Tables/Vtable.GFS ./Vtable
        ./link_grib.csh ~/WRF/GFS/
        vi namelist.wps 
        </copy> 
        
    
    **請根據下載的檔案變更下方的所有值**。
    
    *   `start_date` 格式為：Year-Month-Day\_hour:minute:second
    *   `end_date` 格式為：Year-Month-Day\_hour:minute:second
    *   `interval seconds`：時間變化的氣象輸入檔案之間的整數秒數。沒有預設值。
    
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
        
    
    **若要結束，只要按 Esc 鍵，然後按 shift：後面接著 wq Enter**
    
    **執行：**
    
        <copy>
        ./ungrib.exe
        rm ./METGRID.TBL 
        ln -s metgrid/METGRID.TBL.ARW ./METGRID.TBL
        ./metgrid.exe
        </copy>
        
7.  您可以使用 ncview 和 skintemp 查看結果，查看下載的資料值。
    
        <copy>
        ncview met_em.d01.2020-12-09_00\:00\:00.nc 
        </copy>
        
    
    ![ncview 的快照](images/nc22.png)
    

## 作業 4：執行 Real 與 WRF

1.  我們擁有內嵌的資料來代表一個地理位置，因此我們新增了氣象資料，最後再使用真實資料執行 WRF。
    
        <copy>
        cd ~/WRF/WRF-4.1.5
        cd run
        rm met_em*
        ln -s ../../WPS-4.1/met_em* .  
        vi namelist.input
        </copy>
        
2.  在 namelist.input 檔案中，我們需要編輯內容來反映我們的地理區域 / 網域以及日期與時間間隔。  
    **請依據您的實驗將所有值都變更為按讚**。
    
    *   `run_days`：執行時間 (天)。
    *   `run_hours`：執行時間 (以小時計)。**注意**：如果超過 1 天，您可以使用 run\_days 和 run\_hours 或僅使用 run\_hours。例如，如果總執行長度為 36 小時，您可以設定 run\_days = 1，而 run\_hours = 12，或 run\_days = 0，而 run\_hours = 36。
    *   `run_minutes`：執行時間 (以分鐘計)。
    *   `run_seconds`：執行時間 (秒)。
    *   `start_year`：開始時間為 4 位數的年份。
    *   `start_month`：開始時間的 2 位數月份。
    *   `start_day`：開始時間的 2 位數日。
    *   `start_hour`：開始時間的 2 位數小時。
    *   `end_year`：結束時間為 4 位數的年份。
    *   `end_month`：結束時間的 2 位數月份。
    *   `end_day`：結束時間的 2 位數日。
    *   `end_hour`：結束時間的 2 位數小時。
    *   `interval_seconds`：內送實際資料之間的時間間隔，即橫向邊界條件檔案之間的間隔。
    *   `e_we`：x (west\_east) 方向 (已分段維度) 的結束索引。
    *   `e_sn`：Y (南北) 方向 (已分割維度) 的結束索引。
    *   `e_vert`：z (垂直) 方向的結束索引 (已分段維度 -- 這表示完整層級)。
    *   `dx`：X 方向的網格長度 (以公尺為單位)。
    *   `dy`：格線長度 (以公尺為單位)。
    
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
        
    
    **若要結束，只要按 Esc 鍵，然後按 shift：後面接著 wq Enter**
    
3.  現在我們終於可以執行 real.exe 和 wrf.exe。這個程式需要一些時間執行 。為了協助加快此速度，我們將使用 MPI 在多核心形狀上執行程式。請記住，本指南是使用 VM.Standard2.16 資源配置編寫的，因此我們將在 2 個核心上執行 real.exe。
    
        <copy>
        cat /proc/cpuinfo #To get cpu infor (use if you forgot your OCI shape).
        mpirun -n 2 ./real.exe #This has real.exe running on two cores
        cat rsl.out.0000 #Use this command check for errors
        </copy>
        
4.  我們可以使用 ncview 查看結果，然後查看 T2 以查看下載的資料值。
    
        <copy>
        ncview wrfinput_d01
        </copy>
        
    
    在這裡您可以看到目標區域的溫度，單位為 2 公尺 。 ![ncview 的快照](images/nc23.png)
    
5.  我們有自己的意見，現在可以使用它來產生預測。我們將使用 10 個核心來執行 real.exe。
    
        <copy>
        mpirun -n 10 ./wrf.exe #This will run on 10 cores
        tail -F rsl.out.0000 #can be used to check for errors and progress.
        </copy>
        
6.  您可以使用 ncview 查看我們的預測結果，如下所示：
    
        <copy>
        ncview wrfout_d01_2020-11-20_06\:00\:00 
        </copy>
        
    
    ![ncview 的快照](images/nc24.png)  
    從我們的預測中，您可以看到我們在 6 小時後查看相同的區域時，發生了某些變更。探索有許多變數，因此請盡情享受。
    

## 確認

*   **作者** - Big Compute 解決方案工程師 Brian Bennett
*   **上次更新者 / 日期** - Big Compute 的 Brian Bennett，2020 年 12 月