# WRF/WPSの設定、インストール、および実行

## 概要

このラボでは、OCIインフラストラクチャでのWRFの設定および実行方法について説明します。このガイドは、バニラ Ubuntuイメージを、実験の実行と気象シミュレーションの実行が可能な環境に変換する方法を示しています。また、設定方法を学習せずにWRFを実行したいだけのカスタム イメージも提供します。

[WRFカスタム・イメージ](https://objectstorage.us-ashburn-1.oraclecloud.com/p/lRqMYYN5VTdSgBz9f8nv7Tz5mzMGMqr7wlN2Y_q6g6GHmTdc9GX8lokgmTui81BA/n/hpc_limited_availability/b/Demo_Materials/o/WRF_DEMOV2)をダウンロードします。

推定ラボ時間: 90分

### 目的

この演習では、次について学習します:

*   依存関係とWRF/WPSをダウンロードしています
*   WRF/WPS用のライブラリのコンパイル
*   WRFおよびWPSのコンパイル
*   Geogridを使用したWRFドメインの作成
*   GFSデータのダウンロードおよびUngribとMetgridの実行
*   実データでのWRFの実行

### 前提条件

この演習では、次のことを前提としています。

*   Oracle Free Tierまたは有料クラウド・アカウント
*   演習1および2で説明されているように、Unbuntu 18.04 CPUインスタンスへのアクセス
*   この演習では、**VM.Standard2.16シェイプ**を使用します。必要に応じて、小さいシェイプを使用できます。

この演習を修了し、WRFを起動して実行するために使用されるテクノロジおよび依存関係が多数あります。次のリンクを自由に使用して、テクノロジをよく理解してください。この演習で使用するテクノロジは次のとおりです。

*   [Intelプロセッサ・ベースのOCIシェイプ](https://docs.cloud.oracle.com/en-us/iaas/Content/Compute/References/computeshapes.htm#vmshapes__vm-standard)
*   [Ubuntu 18.04](https://wiki.ubuntu.com/BionicBeaver/ReleaseNotes)
*   [gfortran 7.5.0](https://gcc.gnu.org/onlinedocs/gcc-7.5.0/gfortran.pdf)
*   [ケツ](https://www.geeksforgeeks.org/cpp-command-in-linux-with-examples/)
*   [gcc](https://gcc.gnu.org/onlinedocs/gcc-3.3.6/gcc/G_002b_002b-and-GCC.html)
*   [チップ](https://www.computerhope.com/unix/ucsh.htm#:~:text=csh%20is%20a%20command%20language,and%20a%20C%2Dlike%20syntax.)
*   [Perl](https://www.perl.org/about.html)
*   [g++](https://www.geeksforgeeks.org/compiling-with-g-plus-plus/)
*   [作成](https://www.gnu.org/software/make/)
*   [NETCDF](https://www.unidata.ucar.edu/software/netcdf/) 4.1.2
*   [LIBPNG](http://www.libpng.org/pub/png/libpng.html) 1.6
*   [ZLIB](https://zlib.net/) 1.2.11
*   [ジャスパー](http://ftp.oregonstate.edu/.2/lfs-website/blfs/view/svn/general/jasper.html) 1.900.1
*   [MPICH](https://www.mpich.org/) 3.3.2 (パラリズム用)
*   [WRF-4.1.5](https://github.com/wrf-model/WRF/releases/tag/v4.1.5)
*   [WPS-4.1](https://github.com/wrf-model/WPS/releases/tag/v4.1)
*   [Ncview](http://meteora.ucsd.edu/~pierce/ncview_home_page.html)
*   [m4](https://www.gnu.org/software/m4/m4.html)
*   [上部](https://htop.dev/)
*   [tigerVNC](https://tigervnc.org/)
*   [pvダイアログ](https://www.geeksforgeeks.org/pv-command-in-linux-with-examples/)
*   [深夜コマンダ](https://midnight-commander.org/)(オプション)

## タスク1: Gnomeデスクトップの設定

1.  使用できるデスクトップ・テクノロジは多数ありますが、このガイドではUbuntuのデフォルトであるため、Gnomeデスクトップの設定と設定について説明します。次のステップは、Ubuntu 18.04インスタンスのGnomeデスクトップをダウンロードしてインストールし、構成します。
    
        <copy>
        sudo apt update
        sudo apt upgrade -y
        sudo reboot # Wait about 60 seconds before proceeding
        sudo apt install xserver-xorg-core -y
        sudo apt install tigervnc-standalone-server tigervnc-xorg-extension tigervnc-viewer -y
        sudo apt install ubuntu-gnome-desktop -y
        sudo systemctl start gdm
        sudo systemctl enable gdm
        vncserver # DO NOT MAKE READ ONLY and password should be no greater than 8 characters
        vncserver -kill :*
        </copy>
        
2.  vi ~/.vnc/xstartup  
    **次を入力してください:**
    
        <copy>
        #!/bin/sh  
        [ -x /etc/vnc/xstartup ] && exec /etc/vnc/xstartup  
        [ -r $HOME/.Xresources ] && xrdb $HOME/.Xresources  
        vncconfig -iconic &  
        dbus-launch --exit-with-session gnome-session &  
        </copy>
        
    
    **終了するには、Esc、Shift、wq Enterの順に押します。**
    
    次のようになります。  
    ![vimエディタのスクリーンショット](images/vnc-vi.png)
    
3.  xstartupファイルを実行可能にし、vncサーバーを実行します。
    
        <copy>
        sudo chmod +x ~/.vnc/xstartup
        vncserver -geometry 1440x900
        </copy>
        

## タスク2: Gnomeデスクトップへの接続

[TigerVNC Viewer](https://tigervnc.org/)を使用してインスタンスに接続します。

1.  ローカル・ターミナルを開きます。
    
    インスタンスに関する情報を指定して次のコマンドを実行します: ssh -L 5901:localhost:5901 ubuntu@IPADDRESS
    
    ![sshのスクリーンショット](images/ssh.png)
    
2.  TigerVNCビューアを開きます
    
    「VNC Server: 」セクションに`localhost:1`と入力し、  
    「connect」をクリックします  
    ![TigerVNCビューアのスクリーンショット](images/tigervnc.png)
    
3.  VNCパスワードを入力  
    して「OK」をクリックします  
    ![VNC認証のスクリーンショット](images/vncauth.png)
    
4.  最後に、GNOMEデスクトップが表示され、ここから作業を続行する必要があります。  
    ![Gnomeデスクトップのスクリーンショット](images/gnomedesktop.png)
    

## タスク3: Ubuntuの構成

1.  これで、インスタンスのデスクトップ環境にアクセスできるようになり、WRFを実行するように構成を開始できます。開始するには、依存関係をインストールする必要があります。左上の「アクティビティ」をクリックし、「アプリケーションの表示」をクリックします。端末を検索して開きます。端末で次を実行します。
    
        <copy>
        sudo apt install gfortran -y   
        # Verify install with command: which gfortran    
        # Should return a path: /usr/bin/gfortran  
        sudo apt install cpp    
        # Verify install with command: which cpp    
        # Should return a path: /usr/bin/cpp  
        sudo apt install gcc    
        # Verify install with command: which gcc    
        # Should return a path: /usr/bin/gcc  
        sudo apt install g++ -y  
        sudo apt install make  
        sudo apt install csh  
        sudo apt install perl  
        sudo apt-get install m4  
        sudo apt install htop  
        sudo apt install mc -y    
        sudo apt install ncview -y   
        </copy>
        

## タスク4: WRF用のライブラリのダウンロードとコンパイル

WRFに必要なほとんどの依存関係をインストールしたので、WRFが機能する必要があるライブラリのコンパイルを開始します。

### フォルダ構造の作成およびライブラリのダウンロード

1.  リモート端末で次のコマンドを入力して、必要なフォルダ構造の設定を開始します。
    
        <copy>
        mkdir WRF
        cd WRF
        mkdir downloads
        cd downloads
        </copy>
        
2.  downloadsディレクトリでは、次のコマンドで必要なすべてのライブラリをダウンロードできます。
    
        <copy>
        curl -O ftp://ftp.unidata.ucar.edu/pub/netcdf/old/netcdf-4.1.2.tar.gz  
        curl -O https://www.zlib.net/zlib-1.2.11.tar.gz  
        curl -O ftp://ftp.simplesystems.org/pub/libpng/png/src/libpng16/libpng-1.6.37.tar.gz  
        curl -O https://www2.mmm.ucar.edu/wrf/OnLineTutorial/compile_tutorial/tar_files/jasper-1.900.1.tar.gz  
        curl -O https://www.mpich.org/static/downloads/3.3.2/mpich-3.3.2.tar.gz
        </copy>
        
3.  ファイルを1つずつ解凍する方法として、**ループ**を使用してそれを実行してから、WRFディレクトリに戻ることができます。
    
        <copy>
        for i in *.gz ; do tar -xzf $i ; done
        cd ~/WRF  
        </copy>
        
4.  WRFディレクトリに戻ったため、WRF用のライブラリのフォルダ構造を設定できます。また、このガイドの後半で使用するライブラリ・パスを参照する環境変数も作成します。
    
        <copy>
        mkdir libs  
        cd libs  
        mkdir netcdf  
        mkdir mpich  
        mkdir grib2 (this will be used for the jasper, libpng, and zlib libraries)  
        export LIBDIR=~/WRF/libs
        </copy>
        

### grib2ライブラリのコンパイル

grib2ライブラリは、実際には3つの異なるライブラリ(具体的にはzlib、jasperおよびlibpng)のコンパイルです。

5.  Zlibをコンパイルしています
    
        <copy> 
        cd ~/WRF/downloads
        cd zlib-1.2.11
        ./configure --prefix=$LIBDIR/grib2
        make
        make install
        </copy>
        
6.  libpngのコンパイル
    
        <copy>
        cd ~/WRF/downloads
        cd libpng-1.6.37/
        ./configure --prefix=$LIBDIR/grib2 LDFLAGS="-L$LIBDIR/grib2/lib" CPPFLAGS="-I$LIBDIR/grib2/include"
        make
        make install
        </copy>
        
7.  ジャスパーのコンパイル
    
        <copy>
        cd ~/WRF/downloads
        cd jasper-1.900.1/
        ./configure --prefix=$LIBDIR/grib2
        make
        make install
        </copy>
        

### NetcdfおよびMpichライブラリのコンパイル

8.  netcdfライブラリのコンパイル中
    
        <copy>
        cd ~/WRF/downloads
        cd netcdf-4.1.2/
        ./configure --prefix=$LIBDIR/netcdf --disable-dap --disable-netcdf-4
        make
        make install
        </copy>
        
9.  mpichライブラリのコンパイル中
    
        <copy>
        cd ~/WRF/downloads
        cd mpich-3.3.2/
        ./configure --prefix=$LIBDIR/mpich
        make
        make install
        </copy>
        

## タスク5: WRFおよびWPSのコンパイル

1.  ライブラリのフォルダ構造を設定したので、WRFとWPSのダウンロードとコンパイルを開始できます。次のコード・ブロックは、プログラムをダウンロードして適切な場所に配置します。
    
        <copy>
        cd ~/WRF/downloads
        wget https://github.com/wrf-model/WRF/archive/v4.1.5.tar.gz
        wget https://github.com/wrf-model/WPS/archive/v4.1.tar.gz
        for i in *.gz ; do tar -xzf $i ; done
        mv WRF-4.1.5/ ~/WRF/
        mv WPS-4.1/ ~/WRF/
        </copy>
        
2.  WRFをコンパイルする前に、プログラムがコンパイルしたライブラリを検出して使用できるように、いくつかの環境変数を設定する必要があります。
    
        <copy>
        cd .. or /home/ubuntu/WRF
        cd WRF-4.1.5/
        export NETCDF=$LIBDIR/netcdf
        export PATH=$LIBDIR/mpich/bin:$PATH
        export JASPERLIB=$LIBDIR/grib2/lib
        export JASPERINC=$LIBDIR/grib2/include
        </copy>
        
3.  すべてのライブラリを環境変数に参照したので、最後にWRFをコンパイルできます。この演習では、実際に気象データを収集して使用できるように、WFをコンパイルします。これを、構成セクションとコンパイル・セクションの2つのセクションに分割します。
    
    **構成:**  
    このガイドではネストについて説明しないため、オプション34を選択してgfortran/gccおよびオプション1の使用を選択します。
    
        <copy>
        ./configure
        34 (dmpar)
        1
        vi ~/.bashrc  
            export LIBDIR=/home/ubuntu/WRF/libs  
            export LD_LIBRARY_PATH=$LIBDIR/netcdf/lib:$LD_LIBRARY_PATH   
            export PATH=$LIBDIR/mpich/bin:$PATH
        source ~/.bashrc
        </copy>
        
    
    **コンパイル:**  
    ここでは、WRFをコンパイルし、実行されることをテストします。
    
        <copy>
        ./compile em_real
        cd main
        ./wrf.exe
        ./real.exe
        </copy>
        
4.  WRFがコンパイルされたので、WPSをコンパイルする必要があります。最初にWRFをコンパイルする必要があります。ここでオプション3を選択します。これは、x86インフラストラクチャ(OCI VM.Standard2.16)上のLinuxオペレーティング・システム(Ubuntu)をgfortranコンパイラとともに使用しているためです。
    
        <copy>
        cd /home/ubuntu/WRF/WPS-4.1
        export WRF_DIR=/home/ubuntu/WRF/WRF-4.1.5/
        ./configure
        3
        ./compile
        </copy>
        
5.  WPSがコンパイルされたので、世界の地理をシミュレートするために使用するデータをダウンロードする必要があります。この情報を格納する新しいフォルダを作成します。再推奨の高解像度ファイルを使用しているため、圧縮解除には時間がかかるため、PVダイアログを使用して進捗バーを生成しています。
    
        <copy>
        cd /home/ubuntu/WRF
        mkdir GEOG
        cd GEOG
        wget https://www2.mmm.ucar.edu/wrf/src/wps_files/geog_high_res_mandatory.tar.gz  
        sudo apt-get install pv dialog -y
        (pv -n geog_high_res_mandatory.tar.gz| tar xzf - -C . ) \
        2>&1 | dialog --gauge "Extracting file..." 6 50
        </copy>
        
6.  地理データをダウンロードして抽出したら、いくつかのファイルを正しい場所に移動し、不要な追加ファイルを削除する必要があります。午前0時だけのコマンドを使用しますが、必要に応じて端末コマンドだけを使用できます。次のmcのあとにMidnight Commanderインタフェースを使用するため、**`DO NOT`** `copy the following entire code block into terminal`を使用します。
    
        <copy>
        mc    #Opens Midnight Commander
        click on the left WPS_GEOG
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
        
7.  選択した場所にnamelist.wpsファイルをゼロに調整する必要があります。私は、Woburn Massachusetts USAを中心とした小さなグリッド(10,000 x 10,000メートル)になります。
    
        <copy>
        cd /home/ubuntu/WRF/WPS-4.1
        vi namelist.wps
        </copy>
        
    
    **実験に基づいて、以下のすべての値を好きな値に変更してください**。
    
    *   `max_dom`: シミュレーション内のドメインの合計数(親ドメインを含む)を指定する整数。デフォルト値は1です。
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
            ref_lat   =  42.48,  
            ref_lon   = -71.15,  
            truelat1  =  42.48,  
            truelat2  =  42.48,  
            stand_lon = -71.15,  
            geog_data_path = '/home/ubuntu/WRF/GEOG/'  
            /
        </copy>      
        
    
    **終了するには、\[Esc\]、\[Shift\]の順にクリックし、次に\[wq Enter\]を押します。**  
    **これらの変更はすべて、Woburn MAが震源地である地理的領域を対象としています。**
    
8.  ここでは、地理的領域(このガイドでは中心点としてWoburn MA USAを使用しています)の情報を入力したので、ncviewを使用して、geogridプログラムの実行後に正しい場所があることを確認できます。
    
        <copy>
        ./geogrid.exe
        ncview geo_em.d01.nc
        </copy>
        
    
    2d varを使用してランドマスクを確認し、場所を確認します。イメージに問題がなければ、ドメインの作成が完了します。
    
    ![ncviewのスクリーンショット](images/nc1.png)
    
9.  地域またはドメインを設定した後。ドメインにオーバーレイするには、気象データを取得する必要があります。
    
    1.  Webブラウザでhttps://nomads.ncep.noaa.gov/pub/data/nccf/com/gfs/prod/に移動します。
    2.  最新日付のリンクをクリックします(例: gfs.20201120/今日は2020年11月20日)
    3.  00を選択
    
    実際に何もダウンロードしないでください。そのためのスクリプトを作成します。この演習のデータサイズをより簡単に処理できるように、解像度を低くすると、0p25ではなく0p50になります。追加のストレージがあり、高解像度/より信頼性の高いデータが必要な場合は、0p25を使用します。実行する時間の間、ファイルの数をダウンロードする必要があります。各ファイルは、指定された間隔で1時間のデータです。EXを6時間実行する場合は、gfs.t00z.pgrb2.0p50.f000、gfs.t00z.pgrb2.0p50.f003およびgfs.t00z.pgrb2.0p50.f006が必要です。0p25は1時間のステップで、0p50は3時間のステップです。チュートリアルでは、6時間分のデータのみを使用します。後でWRFの使用がより快適になるようになるにつれて、自由に使ってください。
    
10.  正しいディレクトリに移動し、データをダウンロードするスクリプトを作成します。
    
        <copy>
        cd ~/WRF
        mkdir scripts
        mkdir GFS
        cd scripts
        vi download_gfs.sh
        </copy>
        
11.  スクリプトは次のようになります。
    
        <copy>
        #!/bin/bash  
        
        inputdir=/home/ubuntu/WRF/GFS  
        rm -rf $inputdir  
        mkdir $inputdir  
        
        year=2020  
        month=11  
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
        
    
    **終了するには、Esc、Shift、wq Enterの順に押します。**  
    **このスクリプトは、0p50解決時に日付11/20/20のSIX時間データをダウンロードします。ニーズに合わせて調整してください。**
    
12.  次のコマンドは、スクリプトを実行可能にして実行し、データをダウンロードします。
    
        <copy>
        chmod +x download_gfs.sh
        ./download_gfs.sh
        </copy>
        
13.  これで、データをダウンロードしたので、ドメインをオーバーレイするプロセスを実行できます。これを実現するには、ungribとmetgridを使用します。これを、構成と実行セクションの2つのセクションに分割します。
    
    **構成:**
    
        <copy>
        cd ~/WRF/WPS-4.1
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
        start_date = '2020-11-20_00:00:00',         # start time  
        end_date   = '2020-11-20_06:00:00',         #6 hours later than start time  
        interval_seconds = 10800                    #3 hours worth of seconds interval between steps  
        io_form_geogrid = 2,  
        /
        </copy> 
        
    
    **終了するには、Esc、Shift、wq Enterの順に押します。**
    
    **実行:**
    
        <copy>
        ./ungrib.exe
        ln -s metgrid/METGRID.TBL.ARW ./METGRID.TBL
        ./metgrid.exe
        </copy>
        
14.  ncviewを使用し、skintempを見てダウンロードしたデータの値を確認することで、結果を確認できます。
    
        <copy>
        ncview met_em.d01.2020-11-20_00\:00\:00.nc
        </copy>
        
    
    ![ncviewのスクリーンショット](images/nc2.png)
    

## タスク6: RealおよびWRFの実行

1.  地理的位置を表すためにデータをダウンローディングし、気象データをそれに追加した。
    
        <copy>
        cd ~/WRF/WRF-4.1.5
        cd run
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
    *   `frames_per_outfile`: 出力ファイルを分割する頻度。
    *   `num_metgrid_levels`: WPS出力の垂直レベルの数。
        *   met\_em\*ファイルの1つでncdump -hと入力し、この番号を確認します。
    *   `w_damping`: 垂直速度ダンピング・フラグ(操作用)。1に設定します。
    
        <copy>
        &time_control
        run_days                            = 0,
        run_hours                           = 6,
        run_minutes                         = 0,
        run_seconds                         = 0,
        start_year                          = 2020, 2000, 2000,
        start_month                         = 11,   01,   01,
        start_day                           = 20,   24,   24,
        start_hour                          = 00,   12,   12,
        end_year                            = 2020, 2000, 2000,
        end_month                           = 11,   01,   01,
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
        
    
    **終了するには、Esc、Shift、wq Enterの順に押します。**
    
3.  これで、最終的にreal.exeおよびwrf.exeを実行できます。このプログラムの実行には時間がかかります。これをスピードアップするために、MPIを使用して、シェイプの複数のコアでプログラムを実行します。このガイドはVM.Standard2.16シェイプを使用して記述されているため、2つのコアでreal.exeを実行します。
    
        <copy>
        cat /proc/cpuinfo #To get cpu infor (use if you forgot your OCI shape).
        mpirun -n 2 ./real.exe #This has real.exe running on two cores
        cat rsl.out.0000 #Use this command check for errors
        </copy>
        
4.  ncviewを使用して、入力ファイルを確認します。
    
        <copy>
        ncview wrfinput_d01
        </copy>
        
    
    ここでは、ターゲット領域の温度を2 mで確認できます。 ![ncviewのスクリーンショット](images/nc4.png)
    
5.  入力があり、これを使用して予測を生成できるようになりました。
    
        <copy>
        mpirun -n 10 ./wrf.exe #This will run on 10 cores
        tail -F rsl.out.0000 #can be used to check for errors and progress. 
        </copy>
        
6.  次の方法で予測を確認できます。
    
        <copy>
        ncview wrfout_d01_2020-11-20_06\:00\:00
        </copy> 
        
    
    ![ncviewのスクリーンショット](images/nc5.png)  
    この予測から、6時間後に同じエリアを見たときに変更が生じたことがわかります。探求する変数がたくさんあるので、楽しい。
    
    ここでは、実データで天気予報を実行するようにWRFを設定および構成する方法を学習しました。新しいデータで実験を変更するための作業について、より小さなガイドを設定します。
    
    次の演習に進んで、新しいデータを使用して実験を変更する方法を学習できます。
    

## 確認

*   **作成者** - Big Compute、ソリューション・エンジニア、Brian Bennett氏
*   **最終更新者/日付** - Brian Bennett、Big Compute、2020年12月