# WRFによる実験データの変更

## 概要

これはWRFのセットアップ、インストール、および実行ガイドと比較した小さなガイドです。WRF 4.1.5およびWPS 4.1が既に設定されているカスタム イメージの使用を開始するには、『WRF Setup、 Installation、 and Run Guide』を完了した後、またはダウンロード リンクを使用することを意図しています。

推定ラボ時間: 45分

### 目的

この演習では、次について学習します:

*   Geogridを使用したWRFドメインの作成
*   GFSデータのダウンロードおよびUngribとMetgridの実行
*   実データでのWRFの実行

### 前提条件

この演習では、次のことを前提としています。

*   Oracle Free Tierまたは有料クラウド・アカウント
*   演習1および2で説明するように、Ubuntu 18.04 CPUインスタンスへのアクセス
    *   この演習では、**VM.Standard2.16シェイプ**を使用します。必要に応じて、小さいシェイプを使用できます。
*   WRFのセットアップ、インストール、および実行ガイドを参照するか、[ここ](https://objectstorage.us-ashburn-1.oraclecloud.com/p/lRqMYYN5VTdSgBz9f8nv7Tz5mzMGMqr7wlN2Y_q6g6GHmTdc9GX8lokgmTui81BA/n/hpc_limited_availability/b/Demo_Materials/o/WRF_DEMOV2)に用意されているカスタム・イメージをダウンロードしました。
    *   **ノート**: Ubuntuパスワードはppkkego、vncパスワードはppkkegoです  
        リモート・ターミナルで次のコマンドを使用すると変更できます:
        
            <copy>
            sudo passwd ubuntu
            sudo vncpasswd
            </copy> 
            

## タスク1: Gnomeデスクトップへの接続

[TigerVNC Viewer](https://tigervnc.org/)を使用してインスタンスに接続します。

1.  ローカル・ターミナルを開きます。
    
    インスタンスに関する情報を指定して次のコマンドを実行します: ssh -L 5901:localhost:5901 ubuntu@IPADDRESS ![sshのスクリーンショット](images/ssh.png)
    
2.  vncserverセッションの開始  
    vncserver  
    ![sshのスクリーンショット](images/vncstart.png)
    
3.  TigerVNCビューアを開きます
    
    VNCサーバー: localhost:1  
    次に「connect」をクリックします  
    ![TigerVNCビューアのスクリーンショット](images/tigervnc.png)
    
4.  VNCパスワードを入力  
    して「OK」をクリックします  
    ![VNC認証のスクリーンショット](images/vncauth.png)
    
5.  最後に、GNOMEデスクトップが表示され、ここから作業を続行する必要があります。  
    ![Gnomeデスクトップのスクリーンショット](images/gnomedesktop.png)
    

## タスク2: 地理的領域/ドメインの作成

1.  選択した場所にnamelist.wpsファイルをゼロに調整する必要があります。ヒューストン・テキサス・アメリカを中心とした小さなグリッド(10,000 x 10,000 m)になります。
    
        <copy>
        cd ~/WRF/WPS-4.1
        vi namelist.wps
        </copy>
        
    
    **実験に基づいて、以下のすべての値を好きな値に変更してください**。
    
    *   `max_dom`シミュレーション内のドメインの合計数(親ドメインを含む)を指定する整数。デフォルト値は1です。
    *   `e_we`: グリッドの完全な西東ディメンションを指定する整数。デフォルト値なし。
    *   `e_sn`: グリッドの完全な南北ディメンションを指定する整数。デフォルト値なし。
    *   `dx`: マップ・スケール係数が1であるx方向のグリッド距離(メートル単位)を指定する実際の値。
    *   `dy`: マップ・スケール係数が1であるy方向のグリッド距離(メートル)を指定する実際の値。
    *   `ref_lat`: シミュレーション・ドメイン内の場所(i、j)が認識されている場所(緯度、経度)の緯度部分を指定する実際の値。
    *   `ref_lon`: シミュレーション・ドメイン内の場所(i、j)が認識されている場所(緯度、経度)の経度部分を指定する実際の値。
    *   `truelat1`: Lambert適合投影の最初の真の緯度、またはMercatorと極性立体投影の唯一の真の緯度を指定する実際の値。
    *   `truelat2`: Lambert適合円錐投影の2番目の真の緯度を指定する実際の値。
    *   `stand_lon`: ランバート適合および極立体投影のy軸と平行な経度を指定する実際の値。通常の緯度経度投影では、この値は地球の地理極に関する回転を与えます。
    *   `geog_data_path`: 地理データ・ディレクトリが見つかったディレクトリへのパス(相対または絶対)を示す文字列。
    
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
        
    
    **終了するには、\[Esc\]、\[Shift\]の順にクリックし、次に\[wq Enter\]を押します。**  
    **これらの変更は、震源地としてHouston TXを使用する地理的領域を対象としています。**
    
2.  これで、地理的分野の情報を入力しました(このガイドでは、Houston TX USAを中心点として使用します)。ncviewを使用して、geogridプログラムの実行後に正しい場所があることを確認できます。
    
        <copy>
        ./geogrid.exe
        ncview geo_em.d01.nc
        </copy>
        
3.  2d varを使用してランドマスクを確認し、場所を確認します。イメージに問題がなければ、ドメインの作成が完了します。
    
    ![ncviewのスクリーンショット](images/nc21.png)
    

## タスク3: 地理的領域/ドメインへの気象データの追加

地域またはドメインを設定した後。ドメインにオーバーレイするには、気象データを取得する必要があります。

1.  Webブラウザでhttps://nomads.ncep.noaa.gov/pub/data/nccf/com/gfs/prod/に移動します。
    
2.  最新日付のリンクをクリックします(例: gfs.20201120/今日は2020年11月20日)
    
3.  00を選択します  
    `Don’t actually download anything.` そのためのスクリプトを作成します。この演習のデータサイズをより簡単に処理できるように、解像度を低くすると、0p25ではなく0p50になります。追加のストレージがあり、高解像度/より信頼性の高いデータが必要な場合は、0p25を使用します。実行する時間の間、ファイルの数をダウンロードする必要があります。各ファイルは、指定された間隔で1時間のデータです。EXを6時間実行する場合は、gfs.t00z.pgrb2.0p50.f000、gfs.t00z.pgrb2.0p50.f003およびgfs.t00z.pgrb2.0p50.f006が必要です。0p25は1時間のステップで、0p50は3時間のステップです。チュートリアルでは、6時間分のデータのみを使用します。後でWRFの使用がより快適になるようになるにつれて、自由に使ってください。
    
4.  正しいディレクトリに移動し、事前に作成されたスクリプトを編集してデータをダウンロードできます。
    
        <copy>
        cd ~/WRF
        cd scripts
        vi download_gfs.sh
        </copy>
        
    
    スクリプトは次のようになります。  
    ダウンロードするデータの`year`、`month`および`day`を編集するだけです。
    
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
        
    
    **終了するには、Esc、Shift、wq Enterの順に押します。**  
    **このスクリプトは、0p50解決時に日付12/09/20のSIX時間データをダウンロードします。ニーズに合わせて調整してください。**
    
5.  次のコマンドは、スクリプトを実行可能にして実行し、データをダウンロードします。
    
        <copy>
        chmod +x download_gfs.sh
        ./download_gfs.sh
        </copy>
        
6.  これで、データをダウンロードしたので、ドメインをオーバーレイするプロセスを実行できます。これを実現するには、ungribとmetgridを使用します。これを、構成と実行セクションの2つのセクションに分割します。
    
    **構成:**
    
        <copy>
        cd ~/WRF/WPS-4.1
        rm ./Vtable
        ln -s ungrib/Variable_Tables/Vtable.GFS ./Vtable
        ./link_grib.csh ~/WRF/GFS/
        vi namelist.wps 
        </copy> 
        
    
    **ダウンロードしたファイルに基づいて次の値をすべて変更してください**。
    
    *   `start_date` (Year-Month-Day\_hour:minute:second形式)
    *   `end_date` (Year-Month-Day\_hour:minute:second形式)
    *   `interval seconds`: 時間変動の気象入力ファイル間の秒数。デフォルト値なし。
    
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
        
    
    **終了するには、Esc、Shift、wq Enterの順に押します。**
    
    **実行:**
    
        <copy>
        ./ungrib.exe
        rm ./METGRID.TBL 
        ln -s metgrid/METGRID.TBL.ARW ./METGRID.TBL
        ./metgrid.exe
        </copy>
        
7.  ncviewを使用し、skintempを参照してダウンロードしたデータの値を確認することで、結果を確認できます。
    
        <copy>
        ncview met_em.d01.2020-12-09_00\:00\:00.nc 
        </copy>
        
    
    ![ncviewのスクリーンショット](images/nc22.png)
    

## タスク4: RealおよびWRFの実行

1.  地理的位置を表すためにデータをダウンローディングし、気象データをそれに追加した。
    
        <copy>
        cd ~/WRF/WRF-4.1.5
        cd run
        rm met_em*
        ln -s ../../WPS-4.1/met_em* .  
        vi namelist.input
        </copy>
        
2.  namelist.inputファイルで、地理的領域/ドメインおよび日付と間隔を反映するようにコンテンツを編集する必要があります。  
    **実験に基づいてすべての値を好きな値に変更してください**。
    
    *   `run_days`: ランタイム(日数)。
    *   `run_hours`: 実行時間(時間)。**ノート**: 1日を超える場合は、run\_daysとrun\_hoursの両方またはrun\_hoursのみを使用できます。たとえば、合計実行長が36時間の場合、run\_days = 1、run\_hours = 12、またはrun\_days = 0、run\_hours = 36を設定できます。
    *   `run_minutes`: 分単位で実行時間。
    *   `run_seconds`: 実行時間(秒)。
    *   `start_year`: 開始時間の4桁の年。
    *   `start_month`: 開始時間の2桁の月。
    *   `start_day`: 開始時間の2桁の日付。
    *   `start_hour`: 開始時間の2桁の時間。
    *   `end_year`: 終了時間の4桁の年。
    *   `end_month`: 終了時間の2桁の月。
    *   `end_day`: 終了時間の2桁の日。
    *   `end_hour`: 終了時間の2桁の時間。
    *   `interval_seconds`: 受信実データ間の時間間隔(水平境界条件ファイルの間隔)。
    *   `e_we`: 終了索引(x (west\_east)方向(水平ディメンション)。
    *   `e_sn`: y (南北)方向(水平ディメンション)の終了索引。
    *   `e_vert`: z (垂直)方向の終了索引(水平ディメンション-- これは全レベルを表します)。
    *   `dx`: x方向(メートル単位)のグリッド長。
    *   `dy`: y方向(メートル単位)のグリッド長。
    
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
        
    
    **終了するには、Esc、Shift、wq Enterの順に押します。**
    
3.  これで、最終的にreal.exeおよびwrf.exeを実行できます。このプログラムの実行には時間がかかります。これをスピードアップするために、MPIを使用して、シェイプの複数のコアでプログラムを実行します。このガイドはVM.Standard2.16シェイプを使用して記述されているため、2つのコアでreal.exeを実行します。
    
        <copy>
        cat /proc/cpuinfo #To get cpu infor (use if you forgot your OCI shape).
        mpirun -n 2 ./real.exe #This has real.exe running on two cores
        cat rsl.out.0000 #Use this command check for errors
        </copy>
        
4.  ncviewを使用し、T2を参照してダウンロードしたデータの値を確認することで、結果を確認できます。
    
        <copy>
        ncview wrfinput_d01
        </copy>
        
    
    ここでは、ターゲット領域の温度を2 mで確認できます。 ![ncviewのスクリーンショット](images/nc23.png)
    
5.  入力があり、これを使用して予測を生成できるようになりました。10コアを使用してreal.exeを実行します。
    
        <copy>
        mpirun -n 10 ./wrf.exe #This will run on 10 cores
        tail -F rsl.out.0000 #can be used to check for errors and progress.
        </copy>
        
6.  ncviewによる予測結果は以下のとおりです。
    
        <copy>
        ncview wrfout_d01_2020-11-20_06\:00\:00 
        </copy>
        
    
    ![ncviewのスクリーンショット](images/nc24.png)  
    この予測から、6時間後に同じエリアを見たときに変更が生じたことがわかります。探求する変数がたくさんあるので、楽しい。
    

## 確認

*   **作成者** - Big Compute、ソリューション・エンジニア、Brian Bennett氏
*   **最終更新者/日付** - Brian Bennett、Big Compute、2020年12月