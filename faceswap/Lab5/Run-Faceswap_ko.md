# Faceswap 실행

## 소개

컴퓨팅 인스턴스(Lab 4 참조)를 준비했으므로 Faceswap을 계속 실행할 수 있습니다. 실습에서는 교육 데이터를 컴퓨팅 인스턴스에 복사하고, 교육 데이터에서 면을 추출하고, 추출된 면으로 GAN을 교육하고, 스왑된 면으로 비디오를 변환하는 방법을 배웁니다.

예상 워크샵 시간: 60분(모델 교육 제외)

### 목표

*   학습 데이터(스왑할 면이 있는 사진 또는 비디오)를 컴퓨트 인스턴스로 복사합니다.
*   사진 또는 비디오에서 얼굴 [추출](https://forum.faceswap.dev/viewtopic.php?f=25&t=27)
*   추출된 면으로 GAN [교육](https://forum.faceswap.dev/viewtopic.php?f=27&t=146)
*   소스 비디오의 면 A를 면 B로 [변환](https://forum.faceswap.dev/viewtopic.php?f=24&t=1083)하고 바꿉니다.

### 필요한 사항

*   랩 1부터 랩 4까지 모두 완료

## 작업 1: VNC 데스크탑에 연결

1.  다음 명령을 사용하여 VNC 서버에 대한 SSH 터널을 생성합니다. 여기서 private-key는 전용 OpenSSH 키이고 public\_ip은 Ubuntu 컴퓨트 인스턴스의 전용 IP 주소입니다.

    <copy>ssh -i private-key ubuntu@public_ip -C -L 5901:127.0.0.1:5901</copy>
    

2.  VNC 클라이언트를 사용하여 **127.0.0.1:5901**에 연결합니다. ![](images/tigervnc.PNG " ")
    
3.  암호를 묻는 메시지가 표시되면 VNC 서버 구성 중 생성된 암호를 입력합니다. ![](images/tigervncpwd.PNG " ")
    

원격 Ubuntu 데스크톱이 열립니다. ![](images/vncdesktop.PNG " ")

## 작업 2: VNC 데스크톱에서 Faceswap 열기

1.  데스크탑을 마우스 오른쪽 버튼으로 누르고 **여기에서 터미널 열기**를 선택하여 터미널을 엽니다. ![](images/open-terminal.PNG " ")
    
    터미널이 VNC 데스크톱에서 열립니다.
    
    ![](images/vnc-terminal.PNG " ")
    

터미널을 열 수 없는 경우 로컬 시스템의 터미널에서 다음 명령을 실행합니다.

    <copy>sudo apt remove gnome-terminal</copy>
    

    <copy>sudo apt install gnome-terminal</copy>
    

2.  faceswap 폴더로 이동합니다.

    <copy>cd cd ../faceswap/</copy>
    

다음 명령을 실행하여 Faceswap GUI를 실행합니다.

    <copy>bash faceswap_gui_launcher.sh</copy>
    

Faceswap GUI가 열립니다.

![](images/faceswapGUI.PNG " ")

## 작업 3: 학습 데이터를 컴퓨트 인스턴스에 복사

Faceswap GAN을 훈련하려면 얼굴을 바꾸고 싶은 사람 A와 사람 B의 사진이나 비디오를 제공해야 합니다. 이 예에서는 WinSCP를 사용하여 로컬 시스템에서 개인 A와 개인 B의 두 비디오를 원격 Ubuntu 인스턴스로 복사합니다.

1.  로컬 시스템에 [WinSCP](https://winscp.net/eng/download.php)를 다운로드하고 설치합니다.
    
2.  로컬 시스템에서 WinSCP를 열고 왼쪽 side.F에 비디오가 있는 폴더를 엽니다. ![](images/open-winscp.PNG " ")
    
3.  **새 사이트**를 누릅니다. 새 창이 팝업됩니다. ![](images/winscp-newsite.PNG " ")
    
4.  아래와 같이 _hostname_ 필드에 인스턴스의 **IP address**를 입력하고 _user name_ 필드에 **Ubuntu**를 입력합니다. ![](images/winscp-hostname-username.PNG " ")
    
5.  **고급...** 단추를 누르면
    
6.  창의 왼쪽 부분에서 **SSH** 아래의 **Authentication**을 누릅니다.
    
7.  아래와 같이 3개의 점을 누르고 폴더에서 SSH 전용 키 파일을 선택합니다. ![](images/winscp-ppk.PNG " ")
    
8.  **확인**을 누릅니다.
    
9.  **로그인**을 누릅니다.
    
10.  경고 화면이 나타납니다. **Yes**를 누릅니다. ![](images/winscp-warning.PNG " ")
    
11.  왼쪽에는 로컬 파일 폴더가 표시되고 WinSCP 도구 오른쪽에는 원격 파일 폴더가 표시됩니다. ![](images/winscp-auth-success.PNG " ")
    
12.  오른쪽의 필드를 마우스 오른쪽 버튼으로 누르고 **New(새로 만들기) -> Directory(디렉토리)**를 선택하고 폴더 **src**의 이름을 입력하여 원격 폴더에 **src** 폴더를 만듭니다.
    
13.  왼쪽(로컬 시스템)에서 비디오 두 개를 선택하고 오른쪽의 **src** 폴더(원격 시스템)로 끌어옵니다.
    

## 작업 4: 사진 및 비디오에서 얼굴 추출

첫 번째 단계는 비디오 A 및 비디오 B에서 면을 추출하는 것입니다.

1.  Faceswap GUI에서 **Extract** 탭을 선택합니다.
    
2.  **데이터** 필드에 입력 비디오 또는 입력 이미지가 있는 디렉토리를 선택할 수 있는 **입력 디렉토리**가 표시됩니다. 비디오 버튼(첫 번째, 왼쪽)을 누르고 **src** 폴더에서 개인 A의 입력 비디오를 선택하여 입력 비디오를 선택하면 방금 3단계에서 생성하고 채웁니다.
    

![](images/GUI-inputdir.PNG " ")

**주:** 입력 이미지에서 면을 추출하는 경우 1000~10.000개의 이미지가 있어야 합니다.

3.  **출력 디렉토리** 옆에 있는 폴더 버튼을 누릅니다. 소스 비디오의 추출된 면 이미지가 저장될 폴더를 선택합니다. 디렉토리(예: _faceswap/faces/personA_)를 호출하고 **OK**를 누릅니다.

![](images/GUI-outputdir.PNG " ")

4.  선택 사항: 추출 플러그인에 대한 자세한 내용을 읽어보고 이를 사용하려면 [Faceswap 추출 가이드](https://forum.faceswap.dev/viewtopic.php?t=27)를 참조하십시오. 이 예에서는 기본 플러그인을 사용합니다.
    
5.  **추출**을 누릅니다. 오른쪽의 **미리보기** 탭에 비디오에서 추출된 면이 표시됩니다. 하단 필드에는 Python 추출 스크립트의 상태(예: 초기화된 플러그인, 발견된 이미지, 감지된 면 및 오류가 있는 경우)가 표시됩니다.
    

![](images/GUI-extract.PNG " ")

6.  사람 B의 얼굴 추출에 대해 **4단계**에서 1-5를 반복합니다.
    
7.  선택 사항: GUI를 사용하지 않으려는 경우 다음 명령을 사용하여 터미널에서 면 추출을 실행할 수 있습니다(다른 플러그인을 사용하려는 경우 입력 및 출력 디렉토리와 플러그인만 조정).
    

       <copy>/home/ubuntu/miniconda3/envs/faceswap/bin/python /home/ubuntu/faceswap/faceswap.py extract -i /home/ubuntu/faceswap/src/personA/personA.mp4 -o /home/ubuntu/faceswap/faces/personA -D s3fd -A fan -nm none -rf 0 -min 0 -l 0.4 -sz 512 -een 1 -si 0 -L INFO</copy>
    

![](images/extract-terminal.PNG " ")

## 작업 5: GAN 훈련

이제 비디오에서 사람 A와 사람 B의 얼굴을 추출했으므로 Faceswap GAN을 훈련시킬 수 있습니다.

1.  Faceswap GUI에서 **Train** 탭을 선택합니다.
    
2.  **면** 필드에 **입력 A**가 표시됩니다. 여기서 개인 A의 추출된 면 디렉토리(이 경우 _faceswap/faces/personA_)를 선택합니다.
    

![](images/GUI-faces-input.PNG " ")

3.  아래에 **조정 A** 행이 표시됩니다. 선형 파일에는 각 프레임에서 찾은 모든 면, 특히 면이 있는 위치 및 점 지형이 있는 위치(예: 눈, 코, 입술)에 대한 정보가 들어 있습니다. 추출 단계 중에 생성되며 개인 A 비디오의 출처 폴더에 저장됩니다. 파일 버튼을 누르고 아래와 같이 **src** 폴더에서 개인 A에 대한 정렬 파일을 선택합니다.

![](images/GUI-alignmentA.PNG " ")

4.  개인 B에 대해 포인트 1과 2를 반복합니다.
    
5.  **모델** 필드에서 **모델 디렉토리** 옆에 있는 폴더 버튼을 누릅니다. 교육 모델이 저장될 폴더를 선택합니다. 디렉토리(예: _faceswap/model_)를 호출하고 **OK**를 누릅니다.
    
6.  선택 사항: 강사 또는 "데이터 보강" 옵션에 대해 자세히 알아보고 이를 사용하려면 [Faceswap 교육 가이드](https://forum.faceswap.dev/viewtopic.php?f=27&t=146)를 참조하십시오. 이 예에서는 기본 플러그인을 사용합니다.
    
7.  **교육** 버튼을 눌러 교육을 시작합니다.
    

![](images/GUI-train.PNG " ")

오른쪽의 **미리보기** 탭에서 개인 A와 개인 B의 비디오 프레임에 스왑된 면이 표시됩니다.

**그래프** 탭에는 시간 경과에 따른 손실을 표시하는 그래프가 포함되어 있습니다. 모델을 저장할 때마다 업데이트되지만 "새로고침" 버튼을 눌러 새로 고칠 수 있습니다. 모델에 공급되는 모든 면 배치에 대해 GAN은 재생성하려고 시도한 면을 살펴보고 공급된 실제 면과 비교합니다. 얼마나 잘 생각했는지에 따라 점수(손실 값)를 부여합니다.

8.  선택 사항: GUI를 사용하지 않으려는 경우 다음 명령을 사용하여 터미널에서 모델 교육을 실행할 수 있습니다(다른 트레이너 옵션을 사용하려는 경우 입력 디렉토리, 정렬 파일, 모델 디렉토리 및 트레이너 옵션만 조정).

       <copy>/home/ubuntu/miniconda3/envs/faceswap/bin/python /home/ubuntu/faceswap/faceswap.py train -A /home/ubuntu/faceswap/faces/personA -ala /home/ubuntu/faceswap/src/personA/personA_alignments.fsa -B /home/ubuntu/faceswap/faces/personB -alb /home/ubuntu/faceswap/src/personB/personB_alignments.fsa -m /home/ubuntu/faceswap/model -t original -bs 16 -it 1000000 -s 250 -ss 25000 -ps 100 -L INFO</copy>
    

![](images/terminal-train.PNG " ")

모델 학습이 완료되었는지 확인하는 가장 좋은 방법은 미리보기를 보는 것입니다. 결국 실제 스왑의 모습을 보여줍니다. 손실 값도 확인할 수 있습니다. 손실 값이 0에 가까울수록 완료된 교육 가능성이 높아집니다.

![](images/graph-loss-iterations.PNG " ")

**분석** 탭에서 마지막 열의 값 _EGs/sec_를 확인하면 초당 모델을 통해 처리되는 면 수가 표시됩니다. GPU 코어가 더 강력할수록 _EG/초_가 높아지고 모델이 더 빠르게 수렴됩니다.

9.  nvidia-smi 명령으로 교육하는 동안 GPU 사용량을 확인하십시오. ![](images/nvidia-smi-GPU.PNG " ")
    
    휘발성 GPU 활용률을 보여줍니다.
    
10.  선택 사항: GPU 코어 및 메모리 클럭 속도를 높여 애플리케이션 성능을 높일 수 있습니다. 자세한 내용은 [Nvidia GPU 부스트](https://developer.nvidia.com/blog/increase-performance-gpu-boost-k80-autoboost/)에 대한 Nvidia 블로그 게시물을 참조하십시오.
    

## 작업 6: 스왑된 면으로 비디오 변환

교육이 완료되면 소스 비디오에서 비디오 및 스왑 면 A를 얼굴 B로 변환할 수 있습니다.

1.  Faceswap GUI에서 **Convert** 탭을 선택합니다.
    
2.  **데이터** 필드에 **입력 디렉토리**가 표시됩니다. 여기서 입력 비디오가 있는 디렉토리를 얼굴을 바꾸려는 사람과 함께 선택합니다. 이 경우 입력 비디오는 _/faceswap/src/personA_에 저장됩니다.
    
3.  **출력 디렉토리** 옆에 있는 폴더 버튼을 누릅니다. 출력 비디오가 저장될 폴더를 선택합니다. 디렉토리(예: _/faceswap/outputvideo_)를 호출하고 **OK**를 누를 수 있습니다.
    
4.  **조정** 옆에 있는 파일 버튼을 누르고 얼굴을 스왑할 사람(이 경우 사람 A)으로부터 정렬 파일을 선택합니다. 정렬 파일이 **입력 디렉토리**와 동일한 디렉토리에 있으면 비워 둘 수 있습니다.
    
5.  선택 사항: **참조 비디오**는 이미지에서 비디오로 변환하는 경우에만 필요합니다. 이 경우 비워 둡니다.
    
6.  **모델** 필드에서 **모델 디렉토리** 옆에 있는 폴더 버튼을 누릅니다. 교육 모델이 저장되는 폴더를 선택합니다. 이 경우 _faceswap/model_ 디렉토리입니다. **확인**을 누릅니다.
    
7.  **변환**을 누릅니다.
    

![](images/GUI-convert.PNG " ")

8.  선택 사항: GUI를 사용하지 않으려는 경우 다음 명령을 사용하여 터미널에서 변환을 수행할 수 있습니다(다른 플러그인을 사용하려는 경우 입력 및 출력 디렉토리, 정렬 파일, 모델 디렉토리 및 플러그인만 조정).

       <copy>/home/ubuntu/miniconda3/envs/faceswap/bin/python /home/ubuntu/faceswap/faceswap.py convert -i /home/ubuntu/faceswap/src/personA/personA.mp4 -o /home/ubuntu/faceswap/outputvideo -al /home/ubuntu/faceswap/src/personA/personA_alignments.fsa -m /home/ubuntu/faceswap/model -c avg-color -M extended -w opencv -osc 100 -l 0.4 -j 0 -L INFO</copy>
    

![](images/convert-terminal.PNG " ")

9.  _/faceswap/outputvideo_ 디렉토리에서 사람 B의 스왑된 면이 있는 개인 A의 입력 비디오 프레임을 찾을 수 있습니다. ![](images/outputvideo-dir.PNG " ")
    
    ffmpeg를 사용하여 단일 프레임에서 비디오를 구성합니다.
    
    먼저 ffmpeg를 설치합니다.
    
        <copy>sudo apt install ffmpeg</copy>
        
    
    이제 ffmpeg를 사용하여 단일 프레임을 비디오로 결합하여 _outputvideo_ 디렉토리에 포함된 모든 personA 프레임을 루프합니다. 여기서 _%nd_는 순차적 n자리 숫자를 나타냅니다. 우리의 경우 n=6.
    
        <copy>ffmpeg -i personA_%6d.png -c:v libx264 -vf "fps=25,format=yuv420p" output.mp4</copy>
        
10.  [출력 비디오](https://www.youtube.com/watch?v=b-uKJ89QSnE) 시청
    

## **확인**

*   **작성자/날짜** - Maria Patelkou, HPC 솔루션 아키텍트, Oracle Proposal to Production 프로그램, 2021년 3월
*   **최종 업데이트 수행자/날짜** - Maria Patelkou, HPC 솔루션 아키텍트, Oracle Proposal to Production 프로그램, 2021년 3월