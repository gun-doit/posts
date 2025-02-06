# DCM
> Diagnostic Communication Manager

AUTOSAR 진단 기능을 담당하는 모듈로 Dcm, Dem, Det로 구성되어 있다.
DCM은 AUTOSAR Stack 중 Communication Services Layer에 속하며 진단 서비스의 Request에 대한 서비스 처리 및 Response를 담당한다.
![Image](https://poisonpotato.site/public/1736904388788.png)

### DCM 이란?
- Data Flow와 State를 관리하며 진단기의 진단 요청 수행
- Rest, Session등 상태 변경을 @BswM@에 요청
- Full-, Silent-, No-Communication 상태 등을 @ComM@에 요청
- @DTC@ 정보를 Dem에 요청
- PduR을 통해 진단 데이터를 전송

### Dcm Call Stack
Diagnostic Message는 CAN Controller부터 SW-C까지 아래와 같은 Call Stack을 가진다.
![Image](https://poisonpotato.site/public/1736904538567.png)
