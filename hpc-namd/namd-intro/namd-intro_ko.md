# NAMD 소개

이 Runbook은 Oracle Cloud Infrastructure에 GPU 시스템을 배포하고, NAMD를 설치하고, NAMD 소프트웨어를 사용하여 모델을 실행하는 단계를 제공합니다.

NAMD는 미리 정의된 조건 세트에 따라 생체분자에서 원자의 움직임을 시뮬레이션하는 분자 역학 소프트웨어입니다. 그것은 생활 유기체에서 발생하는 실제 조건을 모방하는 온도, 압력 및 기타 입력의 변화에 노출 될 때 이러한 생물 분자의 행동을 식별하는 데 사용됩니다. NAMD는 단백질 접이식, 단백질-리간드 결합 및 세포막 수송에 있는 본을 설치하기 위하여 이용될 수 있고, 그것을 약 연구와 발견을 위한 아주 유용한 신청 만듭니다.

NAMD는 Charm++ 및 Converse를 기반으로 구축되며 고성능 컴퓨터에서 실행되어 병렬 처리를 실행할 수 있습니다. 그것은 일리노이 대학에 의해 개발되었습니다. 자세한 내용은 [여기](http://charm.cs.illinois.edu/research/moldyn)에서 확인할 수 있습니다.

![](./images/protein.gif)

## **구조**

이 런북의 아키텍처는 공용 서브넷이 있는 OCI VCN 내에서 실행되는 단일 시스템입니다. GPU 인스턴스가 사용되므로 블록 스토리지는 인스턴스에 연결되어 NAMD 애플리케이션과 함께 설치됩니다. 인스턴스는 공용(public) 서브넷에 있으며 ssh를 통해 액세스할 수 있는 공용 IP가 지정됩니다.

![](./images/arch-draft.png)

## 확인

*   **작성자** - 고성능 컴퓨트 팀
*   **기여자** - Chris Iwicki, Harrison Dvoor, Gloria Lee, Selene Song, Bre Mendonca, Samrat Khosla
*   **Last Updated By/Date** - Samrat Khosla, 2020년 10월