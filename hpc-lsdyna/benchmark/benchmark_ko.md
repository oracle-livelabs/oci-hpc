## **벤치마크 예제**

![](images/lemans.png " ")

STAR-CCM+의 성능은 종종 17 및 105백만 개의 셀이 있는 LeMans 벤치마크를 사용하여 측정됩니다. 다음 그래프는 노드 수가 많을수록 런타임에 어떤 영향을 미치는지 보여 주며 확장성은 100%에 가깝습니다. 이 런북에서 설명되지 않은 RDMA 네트워크는 셀/코어 비율이 중단되기 시작하는 경우에만 일반 TCP 실행과 구별하기 시작합니다.

**1,700만 셀**

![](images/RunTime_17M.png " ")

**1억 5백만 셀**

![](images/Scaling_105M.png " ")

## 확인

*   **작성자** - 고성능 컴퓨트 팀
*   **기여자** - Chris Iwicki, Harrison Dvoor, Gloria Lee, Selene Song, Bre Mendonca, Samrat Khosla
*   **Last Updated By/Date** - Samrat Khosla, 2020년 10월