# 환경 초기화

## 소개

이 실습에서는 이 워크샵을 성공적으로 실행하는 데 필요한 모든 구성 요소를 검토하고 시작합니다.

_예상 실습 시간:_ 30분.

### 목표

*   워크샵 환경을 초기화합니다.

### 필수 조건

이 실습에서는 다음 사항이 있다고 가정합니다.

*   Oracle Cloud 계정
*   완료하였습니다:
    *   랩: 설정 준비(_무료 계층_ 및 _유료 테넌트_만 해당)
    *   실습: 환경 설정

> **주:** _가이드 전체에서 사용되는 편리한_ **복사** _기능을 사용하여 복사/붙여넣기를 수행할 때 붙여넣은 후_ **ENTER** _키를 눌러야 합니다. 그렇지 않으면 ENTER 키를 누를 때까지 마지막 행이 버퍼에 유지됩니다._

## 작업 1: 필요한 프로세스가 작동되어 실행 중인지 검증합니다.

1.  이제 원격 데스크톱 세션에 액세스할 때 아래 표시된 대로 진행하여 후속 실습 실행을 시작하기 전에 환경을 검증하십시오. 다음 프로세스가 작동되어 실행 중이어야 합니다.
    
    *   데이터베이스 리스너
        *   리스너 (1521)
        *   LISTCDB2(1522)
    *   데이터베이스 서버 인스턴스
        *   CDB1
        *   CDB2
    
    _SQL Developer Oracle Connections_ 패널에서 아래와 같이 데이터베이스 옆에 있는 _+_ 기호를 눌러 데이터베이스 접속을 테스트할 수 있습니다.
    
    ![](./images/19c_hol_landing.png " ")
    
2.  데스크탑에서 _Terminal_ 아이콘을 눌러 세션을 실행한 후 다음을 실행하여 예상 프로세스가 작동 중인지 검증합니다.
    
        <copy>
        ps -ef|grep LIST|grep -v grep
        ps -ef|grep ora_|grep pmon|grep -v grep
        systemctl status oracle-database
        systemctl status oracle-db-listener
        </copy>
        
    
    ![](./images/check-pmon-up.png " ") ![](./images/check-db-service-up.png " ") ![](./images/check-dblistner-service-up.png " ")
    
    위의 그림과 같이 예상한 모든 프로세스가 출력에 표시되면 다음 작업을 수행할 준비가 된 것입니다.
    
3.  의심스러운 출력, 실패 또는 작동 중지된 구성 요소가 표시되면 이에 따라 서비스를 다시 시작합니다.
    
        <copy>
        sudo systemctl restart oracle-database
        sudo systemctl restart oracle-db-listener
        </copy>
        

## 작업 2: 다중 테넌트 사용 사례에 대한 데이터베이스 초기화

1.  원격 데스크톱 세션에서 _oracle_ 사용자로 아래 블록을 실행하여 랩 아티팩트를 새로고침합니다.
    
        <copy>
        cd ~
        wget -O labs-novnc.zip https://objectstorage.us-ashburn-1.oraclecloud.com/p/jyHA4nclWcTaekNIdpKPq3u2gsLb00v_1mmRKDIuOEsp--D6GJWS_tMrqGmb85R2/n/c4u04/b/livelabsfiles/o/labfiles/labs-novnc.zip
        unzip -qo labs-novnc.zip
        rm -f labs-novnc.zip
        cd labs/multitenant
        chmod +x *.sh
        </copy>
        
    
    ![](./images/init-multitenant.png " ")
    

이제 [다음 실습으로 진행](#next)할 수 있습니다.

## 부록 1: 시작 서비스 관리

1.  데이터베이스 서비스(모든 데이터베이스 및 표준 리스너).
    
    *   시작
    
        <copy>
        sudo systemctl start oracle-database
        </copy>
        
    
    *   중지
    
        <copy>
        sudo systemctl stop oracle-database
        </copy>
        
    
    *   상태
    
        <copy>
        systemctl status oracle-database
        </copy>
        
    
    *   재시작
    
        <copy>
        sudo systemctl restart oracle-database
        </copy>
        
2.  데이터베이스 서비스(비표준 리스너).
    
    *   시작
    
        <copy>
        sudo systemctl start oracle-db-listener
        </copy>
        
    
    *   중지
    
        <copy>
        sudo systemctl stop oracle-db-listener
        </copy>
        
    
    *   상태
    
        <copy>
        systemctl status oracle-db-listener
        </copy>
        
    
    *   재시작
    
        <copy>
        sudo systemctl restart oracle-db-listener
        </copy>
        

## 확인

*   **작성자** - Oracle Database In-Memory 수석 제품 관리자 Andy Rivenes
*   **기여자** - Kay Malcolm, Didi Han, Rene Fontcha
*   **최종 업데이트 수행자/날짜** - Rene Fontcha, LiveLabs 플랫폼 리드, NA 기술, 2021년 10월