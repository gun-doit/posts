# AUTOSAR란?
AUTOmotive Open System ARchitecture(자동차 오픈 시스템 아키텍처)의 약자로, 자동차 소프트웨어 및 전자 제어 장치를 개발한 &r표준 아키텍처&r를 의미한다.

### 오토사 아키텍처 계층
1. Application Layer
2. Runtime Environment(RTE)
3. Basic Software(BSW)

다중 프로세스/스레드가 지원되지 않는 임베디드 시스템상에서 구현되고 동작하기 때문에 &r하위 3개의 계층이 한꺼번에 컴파일되고 링킹&r되어 타깃에 기록된다.

### 오토사 개발 과정
1. 일반적으로 Top-down 방식으로 개발을 진행
2. 시스템 설계자는 @VFB@상에서 SW-C를 설계, SW-C간 Data의 이동은 &rPort와 Interface&r로 정의
3. Configure System이라는 디자인 단계에서 SW-C는 특정 ECU에 할당. ECU Extract과정을 통해 개발 ECU에 설계 정보 전달
4. 개별 ECU는 SW-C간 또는 SW-C와 BSW간 구체적인 인터페이스를 RTE를 통해 구현
Configure System이라는 디자인 단계에서 SW-C는 특정 ECU에 할당. ECU Extract과정을 통해 개발 ECU에 설계 정보 전달
4. 개별 ECU는 SW-C간 또는 SW-C와 BSW간 구체적인 인터페이스를 RTE를 통해 구현

### 오토사 소프트웨어 개발 순서
1. **Configure System**
시스템 설정 단계로 컴포넌트의 구성/연결 등을 정의한다.(@SCD@을 개발)
2. **Implement Componet**
”Configure System” 단계에서 구성한 컴포넌트들에 대한 코드 구현 등을 진행
3. **Extract ECU-Specific Information**
시스템 구성 정보로 부터 특정 제어기 SW를 구현하기 위한 정보만을 추출
4. **Configure ECU**
제어기 관련 설정을 진행 ( ECU Configuration Description 개발 )
5. **Generate Executable**
제어기에서 동작하는 실행 파일을 생성한다. ( 컴파일 및 링크를 통해 실행 이미지 생성) 