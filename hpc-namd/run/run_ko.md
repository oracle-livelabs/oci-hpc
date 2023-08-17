# 응용 프로그램 실행

## 소개

이 연습에서는 응용 프로그램을 실행합니다.

예상 실습 시간: 5분

## 작업: 응용 프로그램 실행

제공된 terraform 스크립트를 사용하여 응용 프로그램을 실행하면 NAMD with CUDA가 /mnt/block/NAMD/NAMD\_2.13\_CUDA 폴더에 설치되고 두 가지 예제 모델은 /mnt/block/work/NAMD\_models 폴더에 제공됩니다.

1.  다음 명령을 통해 CUDA로 NAMD를 실행합니다.
    
        <copy>
        
        namd2 +p<# of cores> +setcpuaffinity +devices <cuda visible devices> +idlepoll +commap <CPU to GPU mapping> <model path> > output.txt
        
        </copy>
        
        
    
    설명: - +p - CPU 코어 수 - +setcpuaffinity - 운영 체제에서 번호가 매겨진 순서대로 사용 가능한 코어에 라운드 로빈 방식으로 스레드/프로세스를 지정합니다. - +devices - GPU 장치 수 - +idlepoll - 유휴 상태에서 잠기는 대신 결과를 위해 GPU 폴링 - +commap - CPU와 GPU의 통신 매핑 - output.txt - 분석과 함께 출력 파일
    
    BM.GPU2.2의 예:
    
        <copy>
        
        namd2 +p26 +devices 0,1 +idlepoll +commap 0,14 /mnt/block/work/NAMD_models/apoa1/apoa1.namd > output.txt
        
        </copy>
        
    
    BM.GPU3.8의 예:
    
        <copy>
        
        namd2 +p52 +devices 0,1,2,3,4,5,6,7 +commap 0,1,2,3,26,27,28,29 /mnt/block/work/NAMD_models/apoa1/apoa1.namd > output.txt
        
        </copy>
        
2.  출력 파일에 대해 ns\_per\_day.py를 실행하여 로깅된 타이밍 명령문에 대해 일별 평균 나노초를 계산합니다. 현재 CPU/GPU 구성을 사용하여 애플리케이션의 성능과 효율성을 식별하는 데 사용됩니다.
    
        <copy>
        
        ns_per_day.py output.txt
        </copy>
        

## 확인

*   **작성자** - 고성능 컴퓨트 팀
*   **기여자** - Chris Iwicki, Harrison Dvoor, Gloria Lee, Selene Song, Bre Mendonca, Samrat Khosla
*   **Last Updated By/Date** - Samrat Khosla, 2020년 10월