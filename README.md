# 선박위치정보조회 서비스

### 프로젝트 개요
- 실시간 선박 추적 프로그램
- 프로젝트 기간 : 2024.09 ~ 2024.12
- 프로젝트 인원 : 1명

### 기술 스택
<img src="https://img.shields.io/badge/vue.js-41B883?style=for-the-badge&logo=vue.js&logoColor=white"><img src="https://img.shields.io/badge/java-007396?style=for-the-badge&logo=OpenJDK&logoColor=white"><img src="https://img.shields.io/badge/springboot-6DB33F?style=for-the-badge&logo=springboot&logoColor=white">![Oracle](https://img.shields.io/badge/Oracle-F80000?style=for-the-badge&logo=oracle&logoColor=white)

### 사용 이유    
- Vue.js : 여러개의 마커가 웹상에 표시되야 했기에 렌더링이 빠른 vue를 선택했습니다.
- Java : java.net으로 TCP 통신을 구현하고 maven에서 AIS 메시지를 파싱하는 의존성을 사용하기 위해 사용했습니다.
- Spring Boot : Spring mvc로 간편하게 api를 구현하기 위해 사용했습니다.


### 프로젝트 소개
- 이 프로그램은 AIS(자동식별시스템)를 활용하여 해양에서 운항하고 있는 선박을 추적하거나 항로를 확인할 수 있고 선박의 국적, 속도, 방향 등과 같은 제원정보도 모니터링할 수 있습니다.

> 프론트엔드 서버     
https://github.com/hhh88261/Marine-vessel-information-App

> 백엔드 서버    
https://github.com/hhh88261/Marine-vessel-information-Server

> API    
https://github.com/hhh88261/Vessel-location-information-coastal-AIS-API

> AIS 서버    
https://github.com/hhh88261/AIS-Custom-Server

### 프로젝트 아키텍처
![Image](https://github.com/user-attachments/assets/9a21127b-2a39-4f6c-b945-d9cda011596c)

### 프로젝트 후기

##### 배운점
- TCP 양방향 통신에 대해 배울 수 있었습니다. AIS 서버가 디코딩 서버에 메시지를 보내고 디코딩 서버가 입력 스트림으로 입력받으면 다시 수신확인 메시지를 보내며 응답하는 방식으로 구현했습니다. 통신이 끝나면 socket.close()로 통신을 종료시키도록 했습니다. 이 기능을 구현하면서 서버와 클라이언트간의 상호작용 방식을 배울 수 있었고, 실시간 데이터 송수신에 왜 TCP 방식이 사용되는지 알 수 있었습니다.
- API 명세서를 작성하는 것에 대해 배울 수 있었습니다. github에 push하면서 '혹시나 다른 사람이 내 api를 본다면 한번에 이해할 수 있을까?'라는 의문점을 가지게되었고 인턴 당시 제 사수님께 코드를 보여드리면서 궁금한 점을 여쭈었습니다. 사수님께서는 단순한 api라도 다른 사용자들 입장에서는 어떤 정보를 어떤 방식으로 요청해야하는지, 어떤 정보를 전달받을 수 있을지, 어떤 형식으로 데이터가 넘어오는지 알 수 없기 때문에 명세서는 꼭 작성해야한다고 당부하셨습니다. 저는 구글링과 다른 github api 저장소를 참고하여 api 명세서를 작성하였고 이 과정을 통해 사용자들의 경험을 개선시킬 수 있는 방법에 대해 생각해볼 수 있었습니다.

##### 어려웠던 점
- 메시지를 인코딩, 디코딩하는 과정을 코드로 구현하는 것이 힘들었습니다. 128비트, 424비트라는 긴 길이의 비트를 각 요소별로 나누어 10진수로 변환하는 과정은 많은 시간을 소요했습니다. 특히 타입 5의 메시지가 수신되었을 때 그 직후에 수신되는 메시지까지
이어서 파싱해야한다는 점을 알지 못했었습니다. AIVDM 공식 홈페이지를 통해 이 사실을 알 수 있었고 문제를 해결하였습니다. 
- 오랫동안 정보가 들어오지 않은 마커를 삭제하는 작업이 어려웠습니다. 많은 양의 마커가 웹상에 그려지다 보니 렌더링 성능 저하가 발생했습니다. setTimeout을 활용하여 5분동안 동일한 mmsi가 수신되지 않으면 삭제하는 로직을 구현하였지만
1초당 수신되는 AIS 메시지가 10건에 달했기에, 5분간의 간격으로는 많은 양의 트래픽을 감당하기에는 어려움이 있었습니다. 마커를 그릴 때 feature를 사용하지 않고 canvas layers 등과 같은 방법을 생각했지만 데드라인을 지키지 못해 해결하지 못했습니다. 



