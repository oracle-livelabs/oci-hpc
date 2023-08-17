# WRF로 실험 데이터 변경

## 소개

WRF 설치, 설치 및 실행 설명서와 비교하여 더 작은 가이드입니다. WRF 설치, 설치 및 실행 안내서를 완료하거나 다운로드 링크를 사용하여 WRF 4.1.5 및 WPS 4.1이 이미 설정된 사용자 지정 이미지를 사용하여 시작한 후에 사용하기 위한 것입니다.

예상 실습 시간: 45분

### 목표

이 실습에서는 다음 내용을 다룹니다.

*   Geogrid로 WRF 도메인 생성
*   GFS 데이터 다운로드 및 Ungrib 및 Metgrid 실행
*   실제 데이터로 WRF 실행

### 필수 조건

이 실습에서는 다음 사항이 있다고 가정합니다.

*   Oracle Free Tier 또는 유료 클라우드 계정
*   실습 1 및 2에 설명된 대로 Ubuntu 18.04 CPU instance에 액세스합니다.
    *   이 실습에서는 **VM.Standard2.16 구성**을 사용합니다. 원하는 경우 작은 모양을 사용할 수 있습니다.
*   WRF 설치, 설치 및 실행 가이드를 살펴본 뒤 또는 [여기](https://objectstorage.us-ashburn-1.oraclecloud.com/p/lRqMYYN5VTdSgBz9f8nv7Tz5mzMGMqr7wlN2Y_q6g6GHmTdc9GX8lokgmTui81BA/n/hpc_limited_availability/b/Demo_Materials/o/WRF_DEMOV2)에서 제공되는 커스텀 이미지를 다운로드하세요.
    *   **주**: Ubuntu 비밀번호는 ppkkego이고 vnc 비밀번호는 ppkkego입니다.  
        원격 터미널에서 다음 명령을 사용하여 변경할 수 있습니다.
        
            <copy>
            sudo passwd ubuntu
            sudo vncpasswd
            </copy> 
            

## 작업 1: Gnome 데스크탑에 연결

[TigerVNC Viewer](https://tigervnc.org/)를 사용하여 인스턴스에 접속합니다.

1.  로컬 터미널 열기
    
    instance와 관련된 정보를 사용하여 다음 명령을 실행합니다. ssh -L 5901:localhost:5901 ubuntu@IPADDRESS ![ssh 스크린샷](images/ssh.png)
    
2.  vncserver 세션  
    vncserver 시작  
    ![ssh 스크린샷](images/vncstart.png)
    
3.  TigerVNC 뷰어 열기
    
    VNC server: localhost:1  
    그런 다음 connect를 누릅니다.  
    ![TigerVNC 뷰어의 스크린샷](images/tigervnc.png)
    
4.  VNC 비밀번호를 입력합니다.  
    그런 다음 OK를 누릅니다.  
    ![VNC 인증 스크린샷](images/vncauth.png)
    
5.  마지막으로 gnome 데스크탑이 제공되며 여기에서 작업을 계속해야 합니다.  
    ![Gnome Desktop 스크린샷](images/gnomedesktop.png)
    

## 작업 2: 지리적 영역/도메인 생성

1.  선택한 위치에서 namelist.wps 파일을 0으로 조정해야 합니다. 그것은 작은 그리드 (10,000 x 10,000 미터) 휴스턴 텍사스 미국 중심점으로 될 것입니다.
    
        <copy>
        cd ~/WRF/WPS-4.1
        vi namelist.wps
        </copy>
        
    
    **시험에 따라 아래의 모든 값을 원하는 대로 변경하십시오**.
    
    *   `max_dom` 시뮬레이션에서 상위 도메인을 포함한 총 도메인 수를 지정하는 정수입니다. 기본값은 1입니다.
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
        ref_lat   =  41.87,  
        ref_lon   = -87.62,  
        truelat1  =  41.87,  
        truelat2  =  41.87,  
        stand_lon = -87.62,  
        geog_data_path = '~/WRF/GEOG/'  
        /  
        </copy>
        
    
    **종료하려면 esc, shift, wq enter를 차례로 누릅니다.**  
    **이러한 모든 변경 사항은 휴스턴 TX가 epicenter인 지리적 영역에 적용됩니다.**
    
2.  이제 지리적 관심 영역에 대한 정보를 입력했으므로(이 가이드는 Houston TX USA를 중심점으로 사용함) ncview를 사용하여 geogrid 프로그램을 실행한 후 올바른 위치가 있는지 확인할 수 있습니다.
    
        <copy>
        ./geogrid.exe
        ncview geo_em.d01.nc
        </copy>
        
3.  2d var를 사용하여 랜드마스크를 검사하여 위치를 확인합니다. 이미지에 만족하면 도메인을 생성합니다.
    
    ![ncview 스크린샷](images/nc21.png)
    

## 작업 3: 지리적 영역/도메인에 기상 데이터 추가

지리적 영역 또는 도메인을 설정한 후 이제 도메인에 오버레이할 기상 데이터를 얻어야 합니다.

1.  웹 브라우저에서 https://nomads.ncep.noaa.gov/pub/data/nccf/com/gfs/prod/로 이동합니다.
    
2.  최신 날짜(예: gfs.20201120/ as today is November 20th, 2020)가 포함된 링크를 누릅니다.
    
3.  00을 선택합니다.  
    `Don’t actually download anything.` 스크립트를 생성합니다. 이 실습의 데이터 크기를 보다 쉽게 처리할 수 있도록 해상도가 낮아 0p25 대신 0p50가 됩니다. 추가 스토리지가 있고 더 높은 해상도/더 신뢰할 수 있는 데이터가 필요한 경우 0p25를 사용합니다. 실행할 시간 동안 파일 수를 다운로드해야 합니다. 각 파일은 지정된 간격으로 1시간의 데이터입니다. 6시간 동안 실행하려면 gfs.t00z.pgrb2.0p50.f000, gfs.t00z.pgrb2.0p50.f003 및 gfs.t00z.pgrb2.0p50.f006가 필요합니다. 0p25는 1시간 단계이고 0p50는 3시간 단계입니다. 튜토리얼에서는 6시간 분량의 데이터만 사용합니다. 나중에 WRF를 사용하여 더 편안하게 되므로 더 많이 사용하십시오.
    
4.  올바른 디렉토리로 이동하고 사전 생성된 스크립트를 편집하여 데이터를 다운로드할 수 있습니다.
    
        <copy>
        cd ~/WRF
        cd scripts
        vi download_gfs.sh
        </copy>
        
    
    스크립트는 다음과 같습니다.  
    다운로드하려는 데이터에 대해 `year`, `month` 및 `day`만 편집하면 됩니다.
    
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
        
    
    **종료하려면 Esc 키를 누른 다음 shift:를 누른 다음 wq를 입력합니다.**  
    **이 스크립트는 날짜 12/09/20의 SIX 시간 데이터를 0p50 해상도로 다운로드합니다. 필요에 맞게 조정하십시오.**
    
5.  다음 명령은 스크립트를 실행 가능하도록 만들고 실행하여 데이터를 다운로드합니다.
    
        <copy>
        chmod +x download_gfs.sh
        ./download_gfs.sh
        </copy>
        
6.  이제 데이터를 다운로드했으므로 도메인을 오버레이하는 프로세스를 진행할 수 있습니다. 우리는 그것을 달성하기 위해 ungrib와 metgrid를 사용합니다. 구성 섹션과 실행 섹션이라는 두 섹션으로 구분됩니다.
    
    **구성:**
    
        <copy>
        cd ~/WRF/WPS-4.1
        rm ./Vtable
        ln -s ungrib/Variable_Tables/Vtable.GFS ./Vtable
        ./link_grib.csh ~/WRF/GFS/
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
        start_date = '2020-12-09_00:00:00',         # start time  
        end_date   = '2020-12-09_06:00:00',         #6 hours later than start time  
        interval_seconds = 10800                    #3 hours worth of seconds interval between steps  
        io_form_geogrid = 2,  
        /
        </copy>
        
    
    **종료하려면 Esc 키를 누른 다음 Shift 키를 누른 다음 wq Enter 키를 누릅니다.**
    
    **실행:**
    
        <copy>
        ./ungrib.exe
        rm ./METGRID.TBL 
        ln -s metgrid/METGRID.TBL.ARW ./METGRID.TBL
        ./metgrid.exe
        </copy>
        
7.  ncview를 사용하고 skintemp를 보고 다운로드한 데이터의 값을 확인하여 결과를 확인할 수 있습니다.
    
        <copy>
        ncview met_em.d01.2020-12-09_00\:00\:00.nc 
        </copy>
        
    
    ![ncview 스크린샷](images/nc22.png)
    

## 태스크 4: 실제 및 WRF 실행

1.  지리적 위치를 나타내는 데이터가 부족하여 기상 데이터가 추가되었습니다. 마지막으로 실제 데이터를 사용하여 WRF를 실행할 시간입니다.
    
        <copy>
        cd ~/WRF/WRF-4.1.5
        cd run
        rm met_em*
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
        
    
    **종료하려면 Esc 키를 누른 다음 Shift 키를 누른 다음 wq Enter 키를 누릅니다.**
    
3.  이제 real.exe 및 wrf.exe를 실행할 수 있습니다. 이 프로그램을 실행하는 데 시간이 걸립니다. 이러한 속도를 높이기 위해 MPI를 사용하여 여러 형태의 코어에서 프로그램을 실행합니다. 이 설명서는 VM.Standard2.16 구성을 사용하여 작성되었으므로 2개 코어에서 real.exe를 실행합니다.
    
        <copy>
        cat /proc/cpuinfo #To get cpu infor (use if you forgot your OCI shape).
        mpirun -n 2 ./real.exe #This has real.exe running on two cores
        cat rsl.out.0000 #Use this command check for errors
        </copy>
        
4.  ncview를 사용하고 T2에서 다운로드한 데이터 값을 확인하여 결과를 확인할 수 있습니다.
    
        <copy>
        ncview wrfinput_d01
        </copy>
        
    
    여기서 대상 영역에 대해 2미터의 온도를 볼 수 있습니다. ![ncview 스크린샷](images/nc23.png)
    
5.  입력을 완료했습니다. 이제 예측을 생성하는 데 사용할 수 있습니다. 10개 코어를 사용하여 real.exe를 실행합니다.
    
        <copy>
        mpirun -n 10 ./wrf.exe #This will run on 10 cores
        tail -F rsl.out.0000 #can be used to check for errors and progress.
        </copy>
        
6.  다음과 같이 ncview를 사용하여 예측 결과를 확인할 수 있습니다.
    
        <copy>
        ncview wrfout_d01_2020-11-20_06\:00\:00 
        </copy>
        
    
    ![ncview 스크린샷](images/nc24.png)  
    예측을 통해 6시간 후 동일한 영역을 볼 때 일부 변경 사항이 발생했음을 알 수 있습니다. 탐색할 변수가 많아서 재미있게 보내십시오.
    

## 확인

*   **작성자** - Big Compute 솔루션 엔지니어인 Brian Bennett
*   **최종 업데이트 수행자/날짜** - Brian Bennett, Big Compute, 2020년 12월