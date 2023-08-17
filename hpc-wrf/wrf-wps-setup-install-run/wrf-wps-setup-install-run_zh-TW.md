# WRF/WPS 設定、安裝與執行

## 簡介

本實驗室將為您介紹如何在 OCI 基礎架構上設定並執行 WRF。本指南旨在說明如何將 vanilla Ubuntu 映像檔轉換為能夠執行實驗並執行天氣模擬的環境。我還會在不學習如何設置的情況下，提供您想要執行 WRF 的自訂影像。

下載 [WRF 自訂映像檔](https://objectstorage.us-ashburn-1.oraclecloud.com/p/lRqMYYN5VTdSgBz9f8nv7Tz5mzMGMqr7wlN2Y_q6g6GHmTdc9GX8lokgmTui81BA/n/hpc_limited_availability/b/Demo_Materials/o/WRF_DEMOV2)。

預估實驗室時間：90 分鐘

### 目標

在此實驗室中，您將瞭解：

*   下載相依性和 WRF/WPS
*   為 WRF/WPS 編譯程式庫
*   編譯 WRF 與 WPS
*   使用 Geogrid 建立 WRF 網域
*   下載 GFS 資料並執行 Ungrib 和 Metgrid
*   以真實資料執行 WRF

### 必要條件

此實驗室假設您具有：

*   Oracle Free Tier 或 Paid Cloud 帳戶
*   依據實驗室 1 和 2 中的說明，存取 Unbuntu 18.04 CPU 執行處理
*   請注意，我們將使用 **VM.Standard2.16 資源配置**在這個實驗室中。如果需要，您可以使用較小的形狀。

這項實驗室將運用多項技術及相依性，讓 WRF 啟動及執行。請隨時隨地使用下列連結，以便更熟悉這項技術。此實驗室使用的技術包括：

*   [以 Intel 處理器為基礎的 OCI 資源配置](https://docs.cloud.oracle.com/en-us/iaas/Content/Compute/References/computeshapes.htm#vmshapes__vm-standard)
*   [Ubuntu 18.04](https://wiki.ubuntu.com/BionicBeaver/ReleaseNotes)
*   [gfortran 7.5.0 版](https://gcc.gnu.org/onlinedocs/gcc-7.5.0/gfortran.pdf)
*   [CPU](https://www.geeksforgeeks.org/cpp-command-in-linux-with-examples/)
*   [密送](https://gcc.gnu.org/onlinedocs/gcc-3.3.6/gcc/G_002b_002b-and-GCC.html)
*   [csh](https://www.computerhope.com/unix/ucsh.htm#:~:text=csh%20is%20a%20command%20language,and%20a%20C%2Dlike%20syntax.)
*   [Perl 版本](https://www.perl.org/about.html)
*   [更多 >>](https://www.geeksforgeeks.org/compiling-with-g-plus-plus/)
*   [製作](https://www.gnu.org/software/make/)
*   [NETCDF](https://www.unidata.ucar.edu/software/netcdf/) 4.1.2
*   [LIBPNG](http://www.libpng.org/pub/png/libpng.html) 1.6
*   [ZLIB](https://zlib.net/) 1.2.11
*   [JASPER](http://ftp.oregonstate.edu/.2/lfs-website/blfs/view/svn/general/jasper.html) 1.900.1
*   [MPICH](https://www.mpich.org/) 3.3.2 (代表同位檢查)
*   [WRF-4.1.5 版](https://github.com/wrf-model/WRF/releases/tag/v4.1.5)
*   [工作台 -4.1](https://github.com/wrf-model/WPS/releases/tag/v4.1)
*   [微觀](http://meteora.ucsd.edu/~pierce/ncview_home_page.html)
*   [m4](https://www.gnu.org/software/m4/m4.html)
*   [頂端](https://htop.dev/)
*   [tigerVNC](https://tigervnc.org/)
*   [PV 對話框](https://www.geeksforgeeks.org/pv-command-in-linux-with-examples/)
*   [Midnight Comander](https://midnight-commander.org/) (選擇性)

## 作業 1：設定 Gnome 桌面

1.  有數種不同的桌面技術可供使用，但本指南將超越設定與確認 Gnome 桌面，因為這是 Ubuntu 的預設值。下列步驟將下載、安裝及設定 Ubuntu 18。04 執行處理的 Gnome 桌面。
    
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
    **請輸入下列內容：**
    
        <copy>
        #!/bin/sh  
        [ -x /etc/vnc/xstartup ] && exec /etc/vnc/xstartup  
        [ -r $HOME/.Xresources ] && xrdb $HOME/.Xresources  
        vncconfig -iconic &  
        dbus-launch --exit-with-session gnome-session &  
        </copy>
        
    
    **若要結束，只要按 Esc 鍵，然後按 shift：後面接著 wq Enter**
    
    它應該會像這樣：  
    ![vim 編輯器的螢幕擷取畫面](images/vnc-vi.png)
    
3.  將 xstartup 檔案設為可執行，然後執行 vnc 伺服器。
    
        <copy>
        sudo chmod +x ~/.vnc/xstartup
        vncserver -geometry 1440x900
        </copy>
        

## 作業 2：連線至 Gnome 桌面

我們將使用 [TigerVNC 檢視器](https://tigervnc.org/)連線至我們的執行處理。

1.  開啟本機終端機
    
    使用與您的執行處理有關的資訊執行下列命令：ssh -L 5901:localhost:5901 ubuntu@IPADDRESS
    
    ![ssh 的畫面擷圖](images/ssh.png)
    
2.  開啟 TigerVNC 檢視器
    
    在 VNC Server：區段中輸入 `localhost:1`  
    ，然後按一下連線  
    ![TigerVNC 檢視器的螢幕擷取畫面](images/tigervnc.png)
    
3.  輸入您的 VNC 密碼  
    ，然後按一下「確定」  
    ![VNC 認證螢幕快照](images/vncauth.png)
    
4.  您將會在最後獲得一個 GNOME 桌面，並繼續在此工作。  
    ![Gnome 桌面的螢幕快照](images/gnomedesktop.png)
    

## 作業 3：設定 Ubuntu

1.  現在，我們可以開始設定執行處理的桌面環境，以執行 WRF。若要開始安裝相依性，我們需要繼續進行。按一下左上角的「活動」，然後按一下「顯示應用程式」。搜尋並開啟終端機。在終端機中執行下列動作。
    
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
        

## 工作 4：下載和編譯 WRF 程式庫

現在，我們安裝了大部分的依賴性，因此需要 WRF，才能開始編譯 WRF 程式庫。

### 正在建立資料夾結構並下載程式庫

1.  在遠端終端機中輸入下列指令，以開始設定所需的資料夾結構。
    
        <copy>
        mkdir WRF
        cd WRF
        mkdir downloads
        cd downloads
        </copy>
        
2.  在下載目錄中，可使用下列指令下載我們所需的所有程式庫。
    
        <copy>
        curl -O ftp://ftp.unidata.ucar.edu/pub/netcdf/old/netcdf-4.1.2.tar.gz  
        curl -O https://www.zlib.net/zlib-1.2.11.tar.gz  
        curl -O ftp://ftp.simplesystems.org/pub/libpng/png/src/libpng16/libpng-1.6.37.tar.gz  
        curl -O https://www2.mmm.ucar.edu/wrf/OnLineTutorial/compile_tutorial/tar_files/jasper-1.900.1.tar.gz  
        curl -O https://www.mpich.org/static/downloads/3.3.2/mpich-3.3.2.tar.gz
        </copy>
        
3.  不用將檔案解壓縮一次，就能使用 **loop** 為我們切換回我們的 WRF 目錄。
    
        <copy>
        for i in *.gz ; do tar -xzf $i ; done
        cd ~/WRF  
        </copy>
        
4.  現在我們重新進入 WRF 目錄，讓我們為 WRF 程式庫設定資料夾結構。我們也會建立環境變數，以參考指南稍後使用的資源庫路徑。
    
        <copy>
        mkdir libs  
        cd libs  
        mkdir netcdf  
        mkdir mpich  
        mkdir grib2 (this will be used for the jasper, libpng, and zlib libraries)  
        export LIBDIR=~/WRF/libs
        </copy>
        

### 正在編譯 grib2 程式庫

grib2 程式庫實際上是三個獨立程式庫 (特別是 zlib、jasper 以及 libpng) 的編譯。

5.  編譯 Zlib
    
        <copy> 
        cd ~/WRF/downloads
        cd zlib-1.2.11
        ./configure --prefix=$LIBDIR/grib2
        make
        make install
        </copy>
        
6.  編譯 libpng
    
        <copy>
        cd ~/WRF/downloads
        cd libpng-1.6.37/
        ./configure --prefix=$LIBDIR/grib2 LDFLAGS="-L$LIBDIR/grib2/lib" CPPFLAGS="-I$LIBDIR/grib2/include"
        make
        make install
        </copy>
        
7.  編譯 jasper
    
        <copy>
        cd ~/WRF/downloads
        cd jasper-1.900.1/
        ./configure --prefix=$LIBDIR/grib2
        make
        make install
        </copy>
        

### 編譯 Netcdf 和 Mpich 程式庫

8.  正在編譯 netcdf 函式庫
    
        <copy>
        cd ~/WRF/downloads
        cd netcdf-4.1.2/
        ./configure --prefix=$LIBDIR/netcdf --disable-dap --disable-netcdf-4
        make
        make install
        </copy>
        
9.  正在編譯 mpich 程式庫
    
        <copy>
        cd ~/WRF/downloads
        cd mpich-3.3.2/
        ./configure --prefix=$LIBDIR/mpich
        make
        make install
        </copy>
        

## 工作 5：編譯 WRF 與 WPS

1.  現在我們已經為程式庫設定資料夾結構，可以開始下載和編譯 WRF 和 WPS。下列程式碼區塊將會下載並將程式放置在適當的位置。
    
        <copy>
        cd ~/WRF/downloads
        wget https://github.com/wrf-model/WRF/archive/v4.1.5.tar.gz
        wget https://github.com/wrf-model/WPS/archive/v4.1.tar.gz
        for i in *.gz ; do tar -xzf $i ; done
        mv WRF-4.1.5/ ~/WRF/
        mv WPS-4.1/ ~/WRF/
        </copy>
        
2.  在能夠編譯 WRF 之前，我們需要設定一些環境變數，讓程式能夠尋找並使用我們編譯來運作的程式庫。
    
        <copy>
        cd .. or /home/ubuntu/WRF
        cd WRF-4.1.5/
        export NETCDF=$LIBDIR/netcdf
        export PATH=$LIBDIR/mpich/bin:$PATH
        export JASPERLIB=$LIBDIR/grib2/lib
        export JASPERINC=$LIBDIR/grib2/include
        </copy>
        
3.  現在，我們已經將所有程式庫參照到環境變數，最終可以編譯 WRF。在此實驗室中，我們將編譯 WRF 以使用真實的氣象資料。我們將分成兩個區段：設定和編譯區段。
    
    **組態：**  
    我們選擇使用 gfortran/gcc 和 option 1 的選項 34，因為本指南中不包含巢狀架構。
    
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
        
    
    **編譯：**  
    我們在此編譯 WRF 並測試 WRF 是否會執行。
    
        <copy>
        ./compile em_real
        cd main
        ./wrf.exe
        ./real.exe
        </copy>
        
4.  現在，WRF 已經完成編譯，我們需要編譯 WPS。WRF 必須先編譯。我們在此選擇選項 3，因為我們在 x86 基礎架構 (OCI VM.Standard2.16) 上使用 Linux 作業系統 (Ubuntu) 以及 gfortran 編譯器。
    
        <copy>
        cd /home/ubuntu/WRF/WPS-4.1
        export WRF_DIR=/home/ubuntu/WRF/WRF-4.1.5/
        ./configure
        3
        ./compile
        </copy>
        
5.  我們需要下載我們用來模擬世界地理位置的資料。我們會建立新的資料夾來存放這項資訊 。我們使用已建議的高解析度檔案來解壓縮，因此我們使用 PV 對話方塊為我們產生進度列。
    
        <copy>
        cd /home/ubuntu/WRF
        mkdir GEOG
        cd GEOG
        wget https://www2.mmm.ucar.edu/wrf/src/wps_files/geog_high_res_mandatory.tar.gz  
        sudo apt-get install pv dialog -y
        (pv -n geog_high_res_mandatory.tar.gz| tar xzf - -C . ) \
        2>&1 | dialog --gauge "Extracting file..." 6 50
        </copy>
        
6.  現在已下載並擷取地理區域資料，我們需要將一些檔案移至正確的位置，並刪除我們不需要的其他檔案。我們會使用午夜命令者進行這項作業，但您可以視需要只使用終端機命令。mc 之後將使用 Midnight Commander 介面，**`DO NOT`** `copy the following entire code block into terminal`。
    
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
        
7.  我們必須在您選擇的位置將 namelist.wps 檔案調整為零。我是美國沃本麻薩諸塞州 (Woburn Massachusetts USA) 的小方格 (10,000 x 10,000 公尺)。
    
        <copy>
        cd /home/ubuntu/WRF/WPS-4.1
        vi namelist.wps
        </copy>
        
    
    **請依據您的實驗，將下方所有的值變更為按讚**。
    
    *   `max_dom`：指定模擬中網域總數 (包括父項網域) 的整數。預設值為 1。
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
            ref_lat   =  42.48,  
            ref_lon   = -71.15,  
            truelat1  =  42.48,  
            truelat2  =  42.48,  
            stand_lon = -71.15,  
            geog_data_path = '/home/ubuntu/WRF/GEOG/'  
            /
        </copy>      
        
    
    **若要結束，請直接按 Esc 鍵，然後按 wq Enter 鍵**  
    **所有這些變更都是針對具有 Woburn MA 做為進入點的地理區域進行。**
    
8.  現在我們把資訊放在感興趣的地理區域 (本指南使用美國伍本 (Woburn MA USA) 做為中心點)，使用 ncview 在執行地理網格程式之後，驗證我們有正確的位置。
    
        <copy>
        ./geogrid.exe
        ncview geo_em.d01.nc
        </copy>
        
    
    使用 2D 變體檢查地形遮罩以驗證位置。如果您對映像檔感到滿意，我們就會完成建立網域。
    
    ![ncview 的快照](images/nc1.png)
    
9.  設定我們的地理區域或網域後。我們現在需要取得氣象資料，才能覆蓋到網域。
    
    1.  在 Web 瀏覽器中，瀏覽至 https://nomads.ncep.noaa.gov/pub/data/nccf/com/gfs/prod/
    2.  按一下最新日期的連結 (例如 gfs.20201120/，因為今天是 2020 年 11 月 20 日)
    3.  選擇 00
    
    別實際下載任何東西。我們會為該建立指令碼。我們將採用較低的解析度，以便更容易處理此實驗室的資料大小，例如 0p50，而不是 0p25。如果您有額外的儲存，並且想要更高解析度 / 更可靠的資料，請使用 0p25。需要下載檔案數的時間長度 。每個檔案都是所指定間隔的一小時資料。如果您要執行 6 小時，您需要 gfs.t00z.pgrb2.0p50.f000、gfs.t00z.pgrb2.0p50.f003 和 gfs.t00z.pgrb2.0p50.f006。0p25 的步驟為 1 小時，而 0p50 則為 3 小時。我們只會在教學課程中使用 6 小時的資料。稍後使用 WRF 時，您可以隨時隨地使用更多功能。
    
10.  瀏覽至正確的目錄並建立命令檔來下載資料。
    
        <copy>
        cd ~/WRF
        mkdir scripts
        mkdir GFS
        cd scripts
        vi download_gfs.sh
        </copy>
        
11.  命令檔將如下：
    
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
        
    
    **若要結束，只要按 Esc 鍵，然後按 shift:wq Enter 鍵**  
    **此指令碼就會下載日期 11/20/20 的 SIX 小時資料，網址為 0p50。請調整以符合您的需求。**
    
12.  下列指令可讓指令碼執行，並加以執行以下載資料：
    
        <copy>
        chmod +x download_gfs.sh
        ./download_gfs.sh
        </copy>
        
13.  已經下載好資料，可以透過我們的網域蓋過資料。我們將使用不賄賂和祝福來達成這一點。我們將分成兩個區段：設定和執行區段。
    
    **設定：**
    
        <copy>
        cd ~/WRF/WPS-4.1
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
        start_date = '2020-11-20_00:00:00',         # start time  
        end_date   = '2020-11-20_06:00:00',         #6 hours later than start time  
        interval_seconds = 10800                    #3 hours worth of seconds interval between steps  
        io_form_geogrid = 2,  
        /
        </copy> 
        
    
    **若要結束，只要按 Esc 鍵，然後按 shift：後面接著 wq Enter**
    
    **執行：**
    
        <copy>
        ./ungrib.exe
        ln -s metgrid/METGRID.TBL.ARW ./METGRID.TBL
        ./metgrid.exe
        </copy>
        
14.  您可以使用 ncview 查看結果並查看 skintemp 查看我們下載的資料值。
    
        <copy>
        ncview met_em.d01.2020-11-20_00\:00\:00.nc
        </copy>
        
    
    ![ncview 的快照](images/nc2.png)
    

## 作業 6：執行 Real 與 WRF

1.  我們擁有內嵌的資料來代表一個地理位置，因此我們新增了氣象資料，最後再使用真實資料執行 WRF。
    
        <copy>
        cd ~/WRF/WRF-4.1.5
        cd run
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
    *   `frames_per_outfile`：分割輸出檔案的頻率。
    *   `num_metgrid_levels`：WPS 輸出中的垂直層次數。
        *   在其中一個 met\_em\* 檔案上鍵入 ncdump -h 以找出此數字。
    *   `w_damping`：垂直速度阻尼旗標 (供作業使用)。設為 1。
    
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
        
    
    **若要結束，只要按 Esc 鍵，然後按 shift：後面接著 wq Enter**
    
3.  現在我們終於可以執行 real.exe 和 wrf.exe。這個程式需要一些時間執行 。為了協助加快此速度，我們將使用 MPI 在多核心形狀上執行程式。請記住，本指南是使用 VM.Standard2.16 資源配置編寫的，因此我們將在 2 個核心上執行 real.exe。
    
        <copy>
        cat /proc/cpuinfo #To get cpu infor (use if you forgot your OCI shape).
        mpirun -n 2 ./real.exe #This has real.exe running on two cores
        cat rsl.out.0000 #Use this command check for errors
        </copy>
        
4.  Lets 使用 ncview 查看我們的輸入檔案。
    
        <copy>
        ncview wrfinput_d01
        </copy>
        
    
    在這裡您可以看到目標區域的溫度，單位為 2 公尺 。 ![ncview 的快照](images/nc4.png)
    
5.  我們有自己的意見，現在可以使用它來產生預測。
    
        <copy>
        mpirun -n 10 ./wrf.exe #This will run on 10 cores
        tail -F rsl.out.0000 #can be used to check for errors and progress. 
        </copy>
        
6.  您可以使用下列方法檢查我們的預測：
    
        <copy>
        ncview wrfout_d01_2020-11-20_06\:00\:00
        </copy> 
        
    
    ![ncview 的快照](images/nc5.png)  
    從我們的預測中，您可以看到我們在 6 小時後查看相同的區域時，發生了某些變更。探索有許多變數，因此請盡情享受。
    
    藉由此，您將學會如何設定與設定 WRF 以執行真實資料的天氣預測。我會設定一個較小的指南，說明如何變更新資料的實驗。
    
    您可以到下一個實驗室學習如何透過新資料改變實驗。
    

## 確認

*   **作者** - Big Compute 解決方案工程師 Brian Bennett
*   **上次更新者 / 日期** - Big Compute 的 Brian Bennett，2020 年 12 月