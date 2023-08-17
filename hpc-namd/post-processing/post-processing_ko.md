# 사후 처리

## 소개

이 실습에서는 VMD(Visual Molecular Dynamics) 소프트웨어를 사용하여 사후 처리를 위한 모델을 분석합니다.

예상 실습 시간: 5분

## 태스크: 사후 처리

1.  사후 처리를 위해 VMD(Visual Molecular Dynamics) 소프트웨어를 사용하여 모델을 분석할 수 있습니다. 다음 명령을 실행하여 VMD를 구성합니다.
    
        <copy>
        ./configure
        cd src
        sudo make install
        </copy>
        
2.  vnc를 사용하는 경우 vncserver를 실행하고 다음과 같이 vnc 암호를 만듭니다.
    
        <copy>
        sudo systemctl start vncserver@:1.service
        sudo systemctl enable vncserver@:1.service
        vncserver
        vncpasswd
        </copy>
        
3.  localhost:5901(터널링이 구성되었는지 확인)을 사용하여 vnc 접속을 시작하고 다음 명령을 실행하여 VMD를 시작합니다.
    
        <copy>
        vmd
        </copy>
        
4.  /mnt/block/work/NAMD\_models에서 apoa1 및 stmv pdb 파일을 열고 모델로 재생을 시작합니다.
    

## 확인

*   **작성자** - 고성능 컴퓨트 팀
*   **기여자** - Chris Iwicki, Harrison Dvoor, Gloria Lee, Selene Song, Bre Mendonca, Samrat Khosla
*   **Last Updated By/Date** - Samrat Khosla, 2020년 10월