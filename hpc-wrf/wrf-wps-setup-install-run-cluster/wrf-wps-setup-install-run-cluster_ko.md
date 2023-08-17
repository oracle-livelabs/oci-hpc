# HPC 클러스터에서 WRF 설정, 설치 및 실행

## 소개

이 실습에서는 OCI 인프라에서 WRF 클러스터를 설정하고 실행하는 방법에 대해 논의하고 안내합니다. 이 설명서는 실험 및 기상 시뮬레이션을 실행할 수 있는 환경을 설정하기 위해 두 개의 노드 HPC 클러스터를 생성하는 방법을 보여줍니다.

예상 실습 시간: 90분

### 목표

이 실습에서는 다음 내용을 다룹니다.

*   2개의 노드 HPC 클러스터 배치
*   종속성 및 WRF/WPS 다운로드
*   WRF/WPS를 위한 라이브러리 컴파일 중
*   WRF 및 WPS 컴파일
*   Geogrid로 WRF 도메인 생성
*   GFS 데이터 다운로드 및 Ungrib 및 Metgrid 실행
*   실제 데이터로 WRF 실행

### 필수 조건

이 실습에서는 다음 사항이 있다고 가정합니다.

*   Oracle 체험판 또는 유료 클라우드 계정
*   이 실습에서는 클러스터에 두 개의 **BM.HPC2.36** 노드를 사용하고 Bastion에 VM.Standard2.4를 사용합니다.

## 작업 1: HPC 인프라 설정

1.  왼쪽에 있는 탐색 메뉴를 사용하여 **솔루션 및 플랫폼** > 마켓플레이스 > 애플리케이션으로 이동합니다.
2.  검색 막대 유형 HPC에서 ENTER/Return 키를 누릅니다.
3.  All Applications 섹션에서 HPC Cluster를 선택한 다음 스택을 실행합니다. 왼쪽 이미지와 오른쪽 이미지가 차례로 나타나야 합니다.  
    ![HPC 이미지 스크린샷](images/hpc2.png)
4.  Create Stack 메뉴에서 이름과 설명을 적절히 제공한 다음 Next를 누릅니다.
5.  AD를 선택하고 SSH 키를 붙여 넣은 다음 CONFIGURE NFS SHARE 상자를 선택된 상태로 둡니다.
6.  배스천의 경우 클러스터와 동일한 AD를 선택하고 **VM.Standard.2.4** 구성을 선택한 후 다음을 누릅니다.
7.  검토 화면에서 입력한 모든 정보를 두 번 확인한 다음 생성을 누릅니다.

## 작업 2: Gnome Desktop 설정

1.  사용할 수 있는 데스크탑 기술에는 여러 가지가 있지만 이 설명서에서는 Oracle Linux의 기본값인 Gnome 데스크탑을 설정하고 구성합니다. 다음 단계에서는 Oracle Linux 7 Bastion 인스턴스용 Gnome Desktop을 다운로드, 설치 및 구성합니다.
    
        <copy>
        sudo yum groupinstall "server with gui"
        Y
        sudo yum install tigervnc-server
        Y
        vncserver # DO NOT MAKE READ ONLY and password should be no greater than 8 characters
        </copy>
        
2.  로컬 터미널 열기  
    instance와 관련된 정보를 사용하여 다음 명령을 실행합니다. ssh -L 5901:localhost:5901 opc@IPADDRESS ![SSH 스크린샷](images/ssh.png)
    
3.  VNC 서버: 섹션에서 TigerVNC 뷰어 유형 `localhost:1`을 엽니다.  
    그런 다음 Connect를 누릅니다.  
    ![TigerVNC 뷰어의 스크린샷](images/tigervnc.png)
    
4.  VNC 비밀번호를 입력합니다.  
    그런 다음 OK를 누릅니다.  
    ![VNC 인증 스크린샷](images/vncauth.png)
    
5.  마지막으로 gnome 데스크탑이 제공되며 여기에서 작업을 계속해야 합니다.  
    ![Gnome Desktop 스크린샷](images/gnomedesktop.png)
    

## 작업 3: Oracle Linux 구성

1.  이제 인스턴스의 데스크톱 환경에 액세스할 수 있으므로 WRF를 실행하도록 구성할 수 있습니다. 시작하려면 종속성을 설치해야 합니다. 왼쪽 위에서 Applications(응용 프로그램)를 누르고 System Tools(시스템 도구)를 누른 다음 Terminal(터미널)을 누릅니다. 터미널에서 다음을 수행합니다.
    
        <copy>
        sudo yum update -y
        sudo yum upgrade -y
        sudo yum group install "Development Tools" -y
        sudo yum install ncview -y
        sudo yum install mc -y
        </copy>
        

## 작업 4: WRF 라이브러리 다운로드 및 컴파일

WRF에 필요한 대부분의 종속성을 설치했으므로 WRF가 작동하는 데 필요한 라이브러리 컴파일을 시작할 수 있습니다.

### 폴더 구조 생성 및 라이브러리 다운로드 중

1.  원격 터미널에서 다음 명령을 입력하여 필요한 폴더 구조 설정을 시작합니다.
    
        <copy>  
        cd /mnt/nfs-share
        mkdir WRF
        cd WRF
        mkdir downloads
        cd downloads
        </copy>
        
2.  다운로드 디렉토리에서 다음 명령을 사용하여 필요한 모든 라이브러리를 다운로드할 수 있습니다.
    
        <copy>
        curl -O ftp://ftp.unidata.ucar.edu/pub/netcdf/old/netcdf-4.1.2.tar.gz  
        curl -O https://www.zlib.net/zlib-1.2.11.tar.gz  
        curl -O ftp://ftp.simplesystems.org/pub/libpng/png/src/libpng16/libpng-1.6.37.tar.gz  
        curl -O https://www2.mmm.ucar.edu/wrf/OnLineTutorial/compile_tutorial/tar_files/jasper-1.900.1.tar.gz  
        curl -O https://www.mpich.org/static/downloads/3.3.2/mpich-3.3.2.tar.gz
        </copy>
        
3.  파일 압축을 하나씩 해제하는 대신 **루프**를 사용하여 WRF 디렉토리로 돌아갈 수 있습니다.
    
        <copy>
        for i in *.gz ; do tar -xzf $i ; done
        cd /mnt/nfs-share/WRF  
        </copy>
        
4.  이제 WRF 디렉토리로 돌아가서 WRF 라이브러리에 대한 폴더 구조를 설정할 수 있습니다. 또한 가이드의 뒷부분에서 사용할 라이브러리 경로를 참조하는 환경 변수를 만듭니다.
    
        <copy>
        mkdir libs  
        cd libs  
        mkdir netcdf  
        mkdir mpich  
        mkdir grib2 # (this will be used for the jasper, libpng, and zlib libraries)  
        export LIBDIR=/mnt/nfs-share/WRF/libs
        </copy>
        

### grib2 라이브러리 컴파일 중

grib2 라이브러리는 실제로 세 개의 별도 라이브러리(특히 zlib, jasper 및 libpng)를 컴파일합니다.

5.  Zlib 컴파일 중
    
        <copy> 
        cd /mnt/nfs-share/WRF/downloads
        cd zlib-1.2.11
        ./configure --prefix=$LIBDIR/grib2
        make
        make install
        </copy>
        
6.  libpng 컴파일 중
    
        <copy>
        cd /mnt/nfs-share/WRF/downloads
        cd libpng-1.6.37/
        ./configure --prefix=$LIBDIR/grib2 LDFLAGS="-L$LIBDIR/grib2/lib" CPPFLAGS="-I$LIBDIR/grib2/include"
        make
        make install
        </copy>
        
7.  jasper를 컴파일하는 중
    
        <copy>
        cd /mnt/nfs-share/WRF/downloads
        cd jasper-1.900.1/
        ./configure --prefix=$LIBDIR/grib2
        make
        make install
        </copy>
        

### Netcdf 및 Mpich 라이브러리 컴파일

8.  netcdf 라이브러리 컴파일 중
    
        <copy>
        cd /mnt/nfs-share/WRF/downloads
        cd netcdf-4.1.2/
        ./configure --prefix=$LIBDIR/netcdf --disable-dap --disable-netcdf-4
        make
        make install
        </copy>
        
9.  mpich 라이브러리 컴파일 중
    
        <copy>
        cd /mnt/nfs-share/WRF/downloads
        cd mpich-3.3.2/
        ./configure --prefix=$LIBDIR/mpich
        make
        make install
        </copy>
        

## 태스크 5: WRF 및 WPS 컴파일

1.  이제 라이브러리에 대한 폴더 구조를 설정했으므로 WRF 및 WPS를 다운로드하고 컴파일할 수 있습니다. 다음 코드 블록은 프로그램을 다운로드하여 해당 위치에 배치합니다.
    
        <copy>
        cd /mnt/nfs-share/WRF/downloads
        wget https://github.com/wrf-model/WRF/archive/v4.1.5.tar.gz
        wget https://github.com/wrf-model/WPS/archive/v4.1.tar.gz
        for i in *.gz ; do tar -xzf $i ; done
        mv WRF-4.1.5/ /mnt/nfs-share/WRF/
        mv WPS-4.1/ /mnt/nfs-share/WRF/
        </copy>
        
2.  WRF를 컴파일하기 전에 프로그램이 작동하기 위해 컴파일된 라이브러리를 찾아 사용할 수 있도록 일부 환경 변수를 설정해야 합니다.
    
        <copy>
        cd .. or /mnt/nfs-share/WRF
        cd WRF-4.1.5/
        export NETCDF=$LIBDIR/netcdf
        export PATH=$LIBDIR/mpich/bin:$PATH
        export JASPERLIB=$LIBDIR/grib2/lib
        export JASPERINC=$LIBDIR/grib2/include
        </copy>
        
3.  이제 환경 변수에 대한 모든 라이브러리를 참조했으므로 WRF를 컴파일할 수 있습니다. 이 실습에서는 WRF를 컴파일하여 수집된 실제 기상 데이터를 사용할 수 있습니다. 이 섹션은 구성 및 컴파일 섹션의 두 섹션으로 나뉩니다.
    
    **구성:**  
    이 가이드에서는 중첩을 다루지 않으므로 gfortran/gcc 및 옵션 1을 사용하는 선택과 함께 선택할 수 있는 옵션 34를 선택합니다.
    
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
        
    
    **컴파일:**  
    WRF를 컴파일하여 실행될지 테스트합니다.
    
        <copy>
        ./compile em_real
        cd main
        ./wrf.exe
        ./real.exe
        </copy>
        
4.  이제 WRF가 컴파일되었으므로 WPS를 컴파일해야 합니다. 먼저 WRF를 컴파일해야 합니다. 여기서는 gfortran 컴파일러와 함께 x86 인프라(OCI BM.HPC2.36)에서 Linux 운영체제(Oracle Linux)를 사용하기 때문에 옵션 3을 선택합니다.
    
        <copy>
        cd /mnt/nfs-share/WRF/WPS-4.1
        export WRF_DIR=/mnt/nfs-share/WRF/WRF-4.1.5/
        ./configure
        3
        ./compile
        </copy>
        
5.  이제 WPS가 컴파일되었으므로 전 세계 지역을 시뮬레이션하는 데 사용할 데이터를 다운로드해야 합니다. 이 정보를 저장할 새 폴더를 생성합니다. 권장 고해상도 파일을 사용하고 있으므로 압축을 해제하는 데 시간이 걸리므로 PV 대화상자를 사용하여 진행 표시줄을 생성합니다.
    
        <copy>
        cd /mnt/nfs-share/WRF/
        mkdir GEOG
        cd GEOG
        wget https://www2.mmm.ucar.edu/wrf/src/wps_files/geog_high_res_mandatory.tar.gz  
        tar xzf geog_high_res_mandatory.tar.gz
        </copy>
        
6.  이제 지리 데이터를 다운로드하여 추출했으므로 일부 파일을 올바른 위치로 이동하고 필요하지 않은 추가 파일을 삭제해야 합니다. 이를 위해 자정 명령어를 사용하지만 원하는 경우 단말기 명령어를 사용할 수 있습니다. 다음 mc는 Midnight Commander 인터페이스를 사용하므로 **`DO NOT`** `copy the following entire code block into terminal`을 사용합니다.
    
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
        
7.  선택한 위치에서 namelist.wps 파일을 0으로 조정해야 합니다. 나는 작은 격자 (10,000 x 10,000 미터) Woburn 매사추세츠 미국 중심점으로 될 것입니다.
    
        <copy>
        cd /mnt/nfs-share/WRF/WPS-4.1
        vi namelist.wps
        </copy>
        
    
    **시험에 따라 아래의 모든 값을 원하는 대로 변경하십시오**.
    
    *   `max_dom`: 시뮬레이션에서 상위 도메인을 포함한 총 도메인 수를 지정하는 정수입니다. 기본값은 1입니다.
    *   `e_we`: 그리드의 서쪽 끝 전체 차원을 지정하는 정수입니다. 기본값이 없습니다.
    *   `e_sn`: 그리드의 전체 남북 차원을 지정하는 정수입니다. 기본값이 없습니다.
    *   `dx`: 맵 배율 계수가 1인 x 방향에서 그리드 거리(미터)를 지정하는 실제 값입니다.
    *   `dy`: 지도 축척 비율이 1인 y 방향에서 그리드 거리(미터)를 지정하는 실제 값입니다.
    *   `ref_lat`: 시뮬레이션 도메인의 (i,j) 위치가 알려진 (위도, 경도) 위치의 위도 부분을 지정하는 실제 값입니다.
    *   `ref_lon`: 시뮬레이션 도메인의 (i, j) 위치가 알려진 (위도, 경도) 위치의 경도 부분을 지정하는 실제 값입니다.
    *   `truelat1`: Lambert 적합 투영에 대한 첫번째 실제 위도를 지정하는 실제 값 또는 Mercator 및 극좌표 고정 투영에 대한 유일한 실제 위도입니다.
    *   `truelat2`: Lambert conformal conic projection에 대한 두번째 true 위도를 지정하는 실제 값입니다.
    *   `stand_lon`: Lambert의 y축과 평행한 경도를 지정하는 실제 값입니다. 일반 위도-경도 투영의 경우 이 값은 지구의 지리적 극에 대한 회전을 제공합니다.
    *   `geog_data_path`: 지리적 데이터 디렉토리를 찾을 수 있는 디렉토리에 상대적 또는 절대 경로를 제공하는 문자열입니다.
    
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
        
    
    **종료하려면 esc, shift, wq enter를 차례로 누릅니다.**  
    **이러한 모든 변경 사항은 Woburn MA가 epicenter인 지리적 영역에 적용됩니다.**
    
8.  이제 지리적 관심 영역에 대한 정보를 입력했으므로(이 가이드는 Woburn MA USA를 중심점으로 사용함) ncview를 사용하여 geogrid 프로그램을 실행한 후 올바른 위치가 있는지 확인할 수 있습니다.
    
        <copy>
        ./geogrid.exe
        sudo ncview geo_em.d01.nc
        </copy>
        
    
    2d var를 사용하여 랜드마스크를 검사하여 위치를 확인합니다. 이미지에 만족하면 도메인을 생성합니다.
    
    ![ncview 스크린샷](images/nc1.png)
    
9.  지리적 영역 또는 도메인을 설정한 후 이제 도메인에 오버레이할 기상 데이터를 얻어야 합니다.
    
    1.  웹 브라우저에서 https://nomads.ncep.noaa.gov/pub/data/nccf/com/gfs/prod/로 이동합니다.
    2.  최신 날짜(예: gfs.20210120/ as today is January 20th, 2021)가 포함된 링크를 누릅니다.
    3.  00을 선택합니다.  
        실제로 아무것도 다운로드하지 않습니다. 이에 대한 스크립트를 작성하겠습니다. 이 실습의 데이터 크기를 보다 쉽게 처리할 수 있도록 해상도가 낮아 0p25 대신 0p50가 됩니다. 추가 스토리지가 있고 더 높은 해상도/더 신뢰할 수 있는 데이터가 필요한 경우 0p25를 사용합니다. 실행할 시간 동안 파일 수를 다운로드해야 합니다. 각 파일은 지정된 간격으로 1시간의 데이터입니다. 6시간 동안 실행하려면 gfs.t00z.pgrb2.0p50.f000, gfs.t00z.pgrb2.0p50.f003 및 gfs.t00z.pgrb2.0p50.f006가 필요합니다. 0p25는 1시간 단계이고 0p50는 3시간 단계입니다. 튜토리얼에서는 6시간 분량의 데이터만 사용합니다. 나중에 WRF를 사용하여 더 편안하게 되므로 더 많이 사용하십시오.
10.  올바른 디렉토리로 이동하여 데이터를 다운로드하는 스크립트를 만들 수 있습니다.
    
        <copy>
        cd /mnt/nfs-share/WRF
        mkdir scripts
        mkdir GFS
        cd scripts
        vi download_gfs.sh
        </copy>
        
11.  이 스크립트는 다음과 같습니다.
    
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
        
    
    **종료하려면 Esc 키를 누른 다음 shift:를 누른 다음 wq를 입력합니다.**  
    **이 스크립트는 날짜 01/20/21에 대한 SIX 시간 데이터를 0p50 해상도로 다운로드합니다. 필요에 맞게 조정하십시오.**
    
12.  다음 명령은 스크립트를 실행 가능하도록 만들고 실행하여 데이터를 다운로드합니다.
    
        <copy>
        chmod +x download_gfs.sh
        ./download_gfs.sh
        </copy>
        
13.  이제 데이터를 다운로드했으므로 도메인을 오버레이하는 프로세스를 진행할 수 있습니다. 우리는 그것을 달성하기 위해 ungrib와 metgrid를 사용합니다. 구성 섹션과 실행 섹션이라는 두 섹션으로 구분됩니다.
    
    **구성:**
    
        <copy>
        cd /mnt/nfs-share/WRF/WPS-4.1
        ln -s ungrib/Variable_Tables/Vtable.GFS ./Vtable
        ./link_grib.csh /mnt/nfs-share/WRF/GFS/
        vi namelist.wps
        </copy>  
        
    
    **다운로드한 파일에 따라 아래 모든 값을 변경하십시오**.
    
    *   `start_date` 형식: Year-Month-Day\_hour:minute:second
    *   `end_date` 형식: Year-Month-Day\_hour:minute:second
    *   `interval seconds`: 시간에 따라 변화하는 기상 입력 파일 사이의 시간(초)입니다. 기본값이 없습니다.
    
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
        
    
    **종료하려면 Esc 키를 누른 다음 Shift 키를 누른 다음 wq Enter 키를 누릅니다.**
    
    **실행:**
    
        <copy>
        ./ungrib.exe
        ln -s metgrid/METGRID.TBL.ARW ./METGRID.TBL
        ./metgrid.exe
        </copy>
        
14.  ncview를 사용하고 skintemp에서 다운로드한 데이터의 값을 확인하여 결과를 확인할 수 있습니다.
    
        <copy>
        sudo ncview met_em.d01.2021-01-20_00:00:00.nc 
        </copy>
        
    
    ![ncview 스크린샷](images/nc2.png)
    

## 태스크 6: 실제 및 WRF 실행

1.  지리적 위치를 나타내는 데이터가 부족하여 기상 데이터가 추가되었습니다. 마지막으로 실제 데이터를 사용하여 WRF를 실행할 시간입니다.
    
        <copy>
        cd /mnt/nfs-share/WRF/WRF-4.1.5
        cd run
        ln -s ../../WPS-4.1/met_em* .
        vi namelist.input
        </copy>
        
2.  namelist.input 파일에서 지리적 영역/도메인과 날짜 및 간격을 반영하도록 내용을 편집해야 합니다.  
    **실험에 따라 원하는 대로 모든 값을 변경하십시오**.
    
    *   `run_days`: 실행 시간(일)입니다.
    *   `run_hours`: 실행 시간(시간)입니다. **참고**: 1일 이상인 경우 run\_days 및 run\_hours를 사용하거나 run\_hours만 사용할 수 있습니다. 예를 들어 총 실행 길이가 36시간인 경우 run\_days = 1, run\_hours = 12 또는 run\_days = 0, run\_hours = 36을 설정할 수 있습니다.
    *   `run_minutes`: 실행 시간(분)입니다.
    *   `run_seconds`: 실행 시간(초)입니다.
    *   `start_year`: 시작 시간의 4자리 연도입니다.
    *   `start_month`: 시작 시간의 2자리 월입니다.
    *   `start_day`: 시작 시간의 2자리 일입니다.
    *   `start_hour`: 시작 시간의 2자리 시간입니다.
    *   `end_year`: 종료 시간의 4자리 연도입니다.
    *   `end_month`: 종료 시간의 2자리 월입니다.
    *   `end_day`: 종료 시간의 2자리 일입니다.
    *   `end_hour`: 종료 시간의 2자리 시간입니다.
    *   `interval_seconds`: 수신 실제 데이터 사이의 시간 간격으로, 측면 경계 조건 파일 사이의 간격이 됩니다.
    *   `e_we`: x(west\_east) 방향(스태거된 차원)의 끝 인덱스입니다.
    *   `e_sn`: y(남북) 방향(스태거된 차원)의 종료 인덱스입니다.
    *   `e_vert`: z(세로) 방향의 종료 인덱스(태거된 차원 -- 전체 레벨 참조)
    *   `dx`: x 방향의 그리드 길이(미터)입니다.
    *   `dy`: y 방향의 그리드 길이(미터)입니다.
    *   `frames_per_outfile`: 출력 파일을 분할하는 빈도입니다.
    *   `num_metgrid_levels`: WPS 출력의 세로 레벨 수입니다.
        *   met\_em\* 파일 중 하나에 ncdump -h를 입력하여 이 번호를 알아냅니다.
    *   `w_damping`: 수직 속도 댐핑 플래그(작동 시 사용) 1로 설정합니다.
    
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
        
    
    **종료하려면 Esc 키를 누른 다음 Shift 키를 누른 다음 wq Enter 키를 누릅니다.**
    
3.  이제 real.exe 및 wrf.exe를 실행할 수 있습니다. 이 프로그램을 실행하는 데 시간이 걸립니다. 이러한 속도를 높이기 위해 MPI를 사용하여 HPC 클러스터의 여러 코어 및 노드에서 프로그램을 실행합니다. 이 설명서는 배스천 서버에 VM.Standard2.4 구성을 사용하여 작성되었으므로 2개 코어에서 real.exe를 실행합니다.
    
        <copy>
        cat /proc/cpuinfo #To get cpu infor (use if you forgot your OCI shape).
        mpirun -n 2 ./real.exe #This has real.exe running on two cores
        cat rsl.out.0000 #Use this command check for errors
        </copy>
        
4.  ncview를 사용하여 입력 파일을 살펴 보겠습니다.
    
        <copy>
        sudo ncview wrfinput_d01
        </copy>
        
    
    여기서 대상 영역에 대해 2미터의 온도를 볼 수 있습니다. ![ncview 스크린샷](images/nc3.png)
    
5.  입력을 완료했습니다. 이제 예측을 생성하는 데 사용할 수 있습니다.
    
        <copy>
        mpirun -np 72 -hosts 172.16.1.2,172.16.1.3 ./wrf.exe #This will run on 72 cores on two nodes with ipaddesses of 172.16.1.2 and 172.16.1.3
        tail -F rsl.out.0000 #can be used to check for errors and progress. 
        </copy>
        
6.  다음을 통해 예측을 확인할 수 있습니다.
    
        <copy>
        sudo ncview wrfout_d01_2020-11-20_06\:00\:00 
        </copy>
        
    
    ![ncview 스크린샷](images/nc4.png)  
    예측을 통해 6시간 후 동일한 영역을 볼 때 일부 변경 사항이 발생했음을 알 수 있습니다. 탐색할 변수가 많아서 재미있게 보내십시오.
    
    이를 통해 HPC 클러스터의 실제 데이터로 날씨 예측을 실행하도록 WRF를 설정하고 구성하는 방법을 배웠습니다. 탐색할 변수와 구성이 많으므로 재미있게 보내십시오.
    

## 확인

*   **작성자** - Big Compute 솔루션 엔지니어인 Brian Bennett
*   **최종 업데이트 수행자/날짜** - Brian Bennett, Big Compute, 2021년 1월