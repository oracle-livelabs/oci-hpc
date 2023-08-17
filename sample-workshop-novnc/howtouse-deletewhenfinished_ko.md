# 단일 실습이 포함된 워크샵

## 지시사항 - 완료 시 이 파일 삭제

1.  Atom 또는 Visual Studio Code에서 샘플 워크샵 템플릿 열기
2.  우리는 5 개의 폴더를 미리 만들었습니다. 워크샵은 여러 실습에서 생성됩니다.
3.  다음과 같은 주석을 제거합니다. _List goals for this lab_
4.  공백에 소문자 폴더 및 파일 이름과 대시를 사용해야 합니다(setup-adb NOT Setup\_ADB).
5.  이미지 이름에는 설명이 포함된 이름이 있어야 합니다. adb1, adb2, adb3뿐만 아니라 핸디캡 접근성을 위해서는 이미지의 모양을 설명하기 위한 이미지 설명이 필요합니다. 모든 소문자와 대시를 기억하십시오.
6.  WMS에서 QA 피드백 문서를 다운로드하십시오. 워크숍을 통해 프로덕션을 앞쪽으로 진행하는 데 필요한 사항을 파악하고 골격을 사용하는 경우 더 빨리 프로덕션에 참여할 수 있습니다.

PS: Readme.md가 필요하지 않습니다. Readme는 최상위 라이브러리 레벨에만 존재합니다. GitHub에서 사용을 추적할 수 없으므로 모든 트래픽을 LiveLabs로 지정합니다. GitHub에 대한 직접 링크를 만들지 마십시오. 워크샵은 매우 인기가 있지만 아무도 모를 수 있도록 추적 할 수 없습니다.

## Oracle Cloud 메뉴 탐색에 대한 절대 경로

**Lab 1: Provision an Instance -> Step 0: Use these Standardized Pictures for Oracle Cloud Navigation (Commonly for Provisioning)** - Oracle Cloud 메뉴 탐색을 위한 공통 스크린샷 리스트가 포함되어 있습니다. 이 섹션을 읽고 해당하는 경우 관련 절대 경로 이미지를 사용하십시오. 이를 통해 Oracle Cloud 사용자 인터페이스 업데이트 시 워크숍이 미래에도 준비될 것입니다.

## 폴더 구조

이 예제의 목표는 하나 이상의 "상위" 워크샵에서 여러 개의 "하위" 워크샵을 생성하는 것입니다. 하위는 상위의 부품으로 구성됩니다.

*   견본 작업장/
    *   개별 랩
        *   prepare-setup/ -- 워크샵에 대한 ORM/Terraform 스택을 다운로드할 수 있는 링크를 포함합니다.
        *   initialize-environment/ -- 프로비전 후 환경을 초기화하는 작업입니다.
        *   프로비저닝/ -- 첫 번째 가상 랩
        *   dataload/ -- 두번째 가상 실험실
        *   query/ -- 세번째 가상 실습
        *   소개/
            *   introduction.md -- 모든 워크샵에 대한 설명입니다. 단 하나의 워크샵만 있으므로 "lab"입니다.
    *   워크샵/
        *   desktop/ -- 워크샵의 _필수_ 데스크톱 버전(모든 랩)
            *   index.html
            *   manifest.json
        *   desktop-advanced/ -- 워크샵의 데스크톱 버전(소개, 초기화 환경, 데이터 로드, 질의)
            *   index.html
            *   manifest.json
        *   desktop-basics/ -- 워크샵의 데스크톱 버전(소개, 초기화 환경, 프로비전)
            *   index.html
            *   manifest.json
        *   tenancy/ -- 워크샵의 _required_ 테넌시 버전(모든 랩)
            *   index.html
            *   manifest.json
        *   tenancy-advanced/ -- 워크샵의 테넌시 버전(소개, 초기화 환경, 데이터 로드, 질의)
            *   index.html
            *   manifest.json
        *   tenancy-basics/ -- 워크샵의 테넌시 버전(소개, 초기화 환경, 프로비전)
            *   index.html
            *   manifest.json
        *   sandbox/ -- 워크샵의 _필수_ 샌드박스 버전(모든 랩)
            *   index.html
            *   manifest.json
        *   sandbox-advanced/ -- 워크샵의 샌드박스 버전(소개, 초기화 환경, 데이터 로드, 질의)
            *   index.html
            *   manifest.json
        *   sandbox-basics/ -- 워크샵의 샌드박스 버전(소개, 초기화 환경, 프로비전)
            *   index.html
            *   manifest.json

### 테넌시 대 샌드박스 대 데스크톱

*   "desktop" - noVNC 원격 데스크탑 세션 _required_ 내에 표시되는 워크샵 설명서
*   "tenancy" - 고객 테넌시를 사용하여 프로비저닝된 워크샵 _필수_
*   "sandbox" - "Reserve Workshop on LiveLabs Sandbox"(녹색 버튼)를 사용하여 프로비저닝된 워크샵 _필수_
*   "desktop-xxxxxxx" - noVNC 원격 데스크탑 세션 내에 표시된 하위 세트 또는 미니 워크샵 설명서
*   "tenancy-xxxxxxx" - 고객 테넌시를 사용하여 프로비전된 하위 세트 또는 미니 워크숍(갈색 버튼)
*   "sandbox-xxxxxxx" - "Reserve Workshop on LiveLabs Sandbox"(녹색 버튼)를 사용하여 프로비전된 하위 세트 또는 미니 워크숍입니다.

### 워크샵 정보

이 워크샵은 개별 실습 6개를 모두 한 순서로 포함합니다.

폴더 구조에는 워크샵을 6개의 전체 실습 세트로 설명하는 "lab" 소개가 포함됩니다. 주: 워크샵의 상위 버전과 하위 버전 각각에 대해 다른 소개가 필요하지 않을 수도 있습니다. 이것은 단지 설명입니다.

product-name-workshop/tenancy 폴더를 살펴보고 manifest.json 파일에서 구조를 확인합니다.

> **주:** 제목에 "Lab n:"을 사용하는 것은 선택 사항입니다.

Prerequisite "lab"은 oracle/learning-library repo의 공통 폴더에 있는 첫번째 연습입니다. 이 실습이 이미 있으므로 대신 RAW/절대 URL을 사용할 수 있습니다.

    "filename": "https://oracle-livelabs.github.io/common/labs/cloud-login/cloud-login-livelabs2.md"        },
    

manifest.json 파일은 계층에서 존재하는 위치와 관련하여 각 연습의 위치를 알아야 합니다. 이 구조에서 실습은 다음과 같이 두 레벨 위에 있습니다.

    "filename": "../../provision/provision.md"
    

### 예를 들면 다음과 같습니다.

이 [APEX Workshop](https://oracle-livelabs.github.io/apex/spreadsheet/)은 단일 실습 [https://github.com/oracle-livelabs/apex/tree/main/spreadsheet](https://github.com/oracle-livelabs/apex/tree/main/spreadsheet)이 포함된 워크샵의 좋은 예입니다.