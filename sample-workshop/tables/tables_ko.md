# LiveLabs의 테이블

## 테이블은 멋지다!

다음과 같이 마크다운에서 테이블을 정의할 수 있습니다.

    | Tables        | Are           | Cool  |
    | ------------- |:-------------:| -----:|
    | **col 3 is**  | right-aligned | $1600 |
    | col 2 is      | *centered*    |   $12 |
    | zebra stripes | ~~are neat~~  |    $1 |
    

결과는 다음과 같습니다.

| 테이블 | 현재 | 아주 멋지네요 |
| --- | :-: | --: |
| **열 3은** | 오른쪽 맞춤 | $1600 |
| 열 2는 | _가운데 배치_ | $12 |
| 얼룩말 | 깔끔함 | $1 |

기본적으로 워크샵 제목과 실습 제목이 연결된 기본 테이블 캡션이 제공됨을 확인할 수 있습니다.

기본값이 마음에 들지 않을 경우 아래 테이블 정의를 추가하여 사용자 고유의 테이블 제목을 제공할 수도 있습니다.

    {: title="My table title"}
    

전체 마크다운은 다음과 같습니다.

    | Tables        | Are           | Cool  |
    | ------------- |:-------------:| -----:|
    | **col 3 is**  | right-aligned | $1600 |
    | col 2 is      | *centered*    |   $12 |
    | zebra stripes | ~~are neat~~  |    $1 |
    {: title="My table title"}
    

이제 테이블은 다음과 같습니다.

| 테이블 | 현재 | 아주 멋지네요 |
| --- | :-: | --: |
| **열 3은** | 오른쪽 맞춤 | $1600 |
| 열 2는 | _가운데 배치_ | $12 |
| 얼룩말 | 깔끔함 | $1 |
| {: title="내 테이블 제목"} |  |  |

보시다시피 번호 매기기가 자동으로 추가됩니다.

쿨하지 않나요?

[LiveLabs Markdown Cheatsheet](https://objectstorage.us-ashburn-1.oraclecloud.com/p/MKKRgodQ0WIIgL_R3QCgCRWCg30g22bXgxCdMk3YeKClB1238ZJXdau_Jsri0nzP/n/c4u04/b/qa-form/o/LiveLabs_MD_Cheat_Sheet.pdf)를 참조할 수도 있습니다.