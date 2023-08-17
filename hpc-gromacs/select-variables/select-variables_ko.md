# 변수 선택

## 소개

이 연습에서는 스택에 대한 변수를 입력합니다.

예상 실습 시간: 10분

## 태스크: 변수 선택

1.  ![](./images/next.png)을 누르고 변수를 입력합니다.
    
    **GPU 노드**:
    
    *   GPU 컴퓨트 노드의 SHAPE: Gromacs 실행에 사용될 컴퓨트 노드의 구성입니다. 최상의 성능을 위해 베어메탈 구성 BM.GPU2.2 또는 BM.GPU3.8을 선택하십시오.
    *   AVAILABILITY DOMAIN: 인스턴스 및 블록 볼륨의 AD. AD에 사용 가능한 GPU가 있어야 합니다.
    *   GPU 노드 수: 필요한 GPU 노드 수입니다.
    *   GPU에 대한 VNC 유형: 헤드노드의 시각화 유형: none, VNC 또는 X11VNC.
    *   PASSWORD FOR THE VNC SESSIONS: Pre/Post 노드에서 VNC 세션을 사용하는 암호입니다.
    
    **블록 옵션**:
    
    *   BLOCK VOLUME SIZE (GB): 공유 블록 볼륨의 크기입니다.
    
    **Gromacs**:
    
    *   URL TO DOWNLOAD Gromacs: Gromacs 2020.1 컴파일된 바이너리의 URL입니다(URL을 다른 버전으로 바꾸거나 나중에 다운로드하려면 비워 두십시오).
    *   모델 TARBALL 다운로드 URL: 실행할 모델의 URL입니다(URL을 다른 모델로 바꾸거나 나중에 다운로드하려면 비워 두십시오).
    *   VMD 시각화 소프트웨어 다운로드 URL: VMD 1.9.3을 다운로드하여 Gromacs 모델을 시각화하는 URL(URL을 다른 시각화 소프트웨어로 바꾸거나 나중에 다운로드하려면 비워 두십시오.)
2.  ![](./images/next.png)을 누릅니다. 정보를 검토하고 다음을 누르십시오. ![](./images/create.png)
    

## 확인

*   **작성자** - 고성능 컴퓨트 팀
*   **기여자** - Chris Iwicki, Harrison Dvoor, Gloria Lee, Selene Song, Bre Mendonca, Samrat Khosla
*   **Last Updated By/Date** - Samrat Khosla, 2020년 10월