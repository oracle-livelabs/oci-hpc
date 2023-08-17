# 설정 준비

## 소개

이 실습에서는 이 워크샵을 실행하는 데 필요한 리소스를 설정하는 데 필요한 ORM(Oracle Resource Manager) 스택 zip 파일을 다운로드하는 방법을 설명합니다.

_예상 실습 시간:_ 10분

### 목표

*   ORM 스택 다운로드
*   기존 VCN(가상 클라우드 네트워크) 구성

### 필수 조건

이 실습에서는 다음 사항이 있다고 가정합니다.

*   Oracle Cloud 계정

## 작업 1: ORM(Oracle Resource Manager) 스택 zip 파일 다운로드

1.  환경을 구축하는 데 필요한 리소스 관리자 zip 파일을 다운로드하려면 아래 링크를 누르십시오.
    
    _주 1:_ 워크샵에 대해 단일 스택 다운로드를 제공하는 경우 이 간단한 표현식을 사용합니다.
    
    *   [샘플 mkplc-freetier.zip](https://objectstorage.us-ashburn-1.oraclecloud.com/p/clKCvIhItftqqFoXdipFq5oukh0jVuwcKEMHVdqOPXN7oUqaoGwPZsimi5pgpfpR/n/natdsecurity/b/stack/o/sample-mkplc-freetier.zip)
    
    _주 2:_ 동일한 워크샵에 대해 여러 개의 Stacks 다운로드를 제공하는 경우 다음과 유사한 조건부 표현식을 사용하십시오. 조건 또는 _type_은 _manifest.json_ 파일의 유효한 항목과 쌍을 이루어야 합니다. _freetier-advanced_ 및 _freetier-basics를 참조하십시오._
    

\- \[sample-mkplc-advanced.zip\](https://objectstorage.us-ashburn-1.oraclecloud.com/p/\_EIwsXv5v6KkKcQldUQixExqAgJCbY826XovJec4I25rc4dHEZW4whrF-nb2QUye/n/natdsecurity/b/stack/o/sample-mkplc-advanced.zip) \- \[sample-mkplc-basics.zip\](https://objectstorage.us-ashburn-1.oraclecloud.com/p/m4wcgeN1hw9D1zV3pgOkbRjwanAt5dIW7QsZS7znZNnHU63vh495UHhkiRtaDJHE/n/natdsecurity/b/stack/o/sample-mkplc-basics.zip)

2.  다운로드 폴더에 저장합니다.

이 스택을 사용하여 인스턴스에 자체 포함/전용 VCN을 생성하는 것이 좋습니다. 권장 사항을 따르려면 _Task 3_으로 건너뜁니다. 기존 VCN을 사용하려면 다음 작업으로 이동하여 필요한 수신 규칙으로 기존 VCN을 업데이트하십시오.

## 작업 2: 기존 VCN에 보안 규칙 추가

이 워크샵에서는 특정 개수의 포트를 사용할 수 있어야 하며, 전용 VCN을 생성하는 기본 ORM 스택 실행을 통해 충족해야 합니다. 기존 VCN/서브넷을 사용하려면 보안 목록에 다음 규칙을 추가해야 합니다.

| 유형 | 소스 포트 | 소스 CIDR | 대상 포트 | 프로토콜 | 설명 |
| :-- | :-: | :-: | :-: | :-: | :-- |
| 수신 | 모두 | 0.0.0.0/0 | 22개 | TCP | SSH |
| 수신 | 모두 | 0.0.0.0/0 | 80개 | TCP | noVNC를 사용하는 원격 데스크탑 |
| 송신 | 모두 | 해당 사항 없음 | 80개 | TCP | 아웃바운드 HTTP 액세스 |
| 송신 | 모두 | 해당 사항 없음 | 443년 | TCP | 아웃바운드 HTTPS 액세스 |
| {: title="필요한 네트워크 보안 규칙 목록"} |  |  |  |  |  |

1.  _네트워킹 >> 가상 클라우드 네트워크_로 이동합니다.
2.  네트워크 선택
3.  리소스 아래에서 보안 목록을 선택합니다.
4.  Create Security List 버튼 아래에서 Default Security Lists를 누릅니다.
5.  Add Ingress Rule 버튼을 누릅니다.
6.  다음을 입력합니다.
    *   소스 유형: CIDR
    *   소스 CIDR: 0.0.0.0/0
    *   IP 프로토콜: TCP
    *   소스 포트 범위: 모두(기본값 유지)
    *   대상 포트 범위: _위 테이블에서 선택_
    *   설명: _위 테이블에서 해당 설명 선택_
7.  Add Ingress Rules 버튼을 누릅니다.
8.  테이블에 나열된 각 포트에 대해 규칙이 생성될 때까지 \[5-7\]단계를 반복합니다.

## 작업 3: 컴퓨트 설정

위의 두 작업에서 세부 정보를 사용하여 실습 _환경 설정_으로 이동하여 ORM(Oracle Resource Manager) 및 다음 옵션 중 하나를 사용하여 워크샵 환경을 설정합니다.

*   스택 생성: _컴퓨트 + 네트워킹_
*   스택 생성: 위의 _작업 2_에 따라 보안 목록이 업데이트된 기존 VCN이 있는 _컴퓨트 전용_

이제 다음 실습을 진행할 수 있습니다.

## 확인

*   **작성자** - Rene Fontcha, LiveLabs 플랫폼 리드, NA 기술
*   **기여자** - Meghana Banka
*   **최종 업데이트 수행자/날짜** - Rene Fontcha, LiveLabs 플랫폼 리드, NA 기술, 2022년 8월