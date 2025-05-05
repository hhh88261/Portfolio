# 선박위치라이브 서비스

### 프로젝트 개요
- 실시간 선박 추적 프로그램
- 프로젝트 기간 : 2024.09 ~ 2024.12
- 프로젝트 인원 : 1명

### 기술 스택
<img src="https://img.shields.io/badge/vue.js-41B883?style=for-the-badge&logo=vue.js&logoColor=white"> : 수백개의 마커가 웹상에 표시되야 했기에 렌더링이 빠른 vue를 선택했습니다  
<img src="https://img.shields.io/badge/java-007396?style=for-the-badge&logo=OpenJDK&logoColor=white"> : java.net으로 소켓 통신을 구현하고 maven에서 AIS 메시지를 파싱하는 의존성을 사용하기 위해 사용했습니다.  
<img src="https://img.shields.io/badge/springboot-6DB33F?style=for-the-badge&logo=springboot&logoColor=white"> : Spring mvc로 간편하게 api를 구현하기 위해 사용했습니다.  
![Oracle](https://img.shields.io/badge/Oracle-F80000?style=for-the-badge&logo=oracle&logoColor=white)  

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

- AIS 기기에서 신호를 원시신호를 받아 128bit 크기의 신호로 가공하는 서버, 그 신호를 수신하여 후처리 후 클라이언트로 전송하는 서버로 분리하였습니다. Java 언어를 기반으로 설계되었습니다.
서버를 분리한 이유는 추후 디코딩 서버 로직이 무거워져 서버를 별도로 확장할 수도 있었기에 원활한 유지보수를 위해 분리하는게 적합하다고 판단했습니다

- DB는 Oracle DB를 사용하고 회원정보, 선박의 제원정보와 항적을 저장합니다.

- 디코딩 서버는 TCP 소켓을 사용해 AIS 메시지를 수신받고 Web Socket을 통해 클라이언트에게 가공공된 AIS 메시지를 수신합니다. AIS 송신 장비는 기본적으로 Web Socket을 지원하지 않고 TCP 또는 UDP 기능만 
지원하기 때문에 일원화된 구조보다는 두 기술을 병행 사용하여 이식성을 높이는 것이 적합할 것이라 판단했습니다.

- API 서버는 로그인, 회원가입, JWT 토큰 관리, 선박 과거항적 검색, 선박 검색과 같은 사용자 요청을 처리합니다. Spring Boot를 사용했습니다.


### ERD
![Image](https://github.com/user-attachments/assets/0eb312bf-c9cd-45cb-acd3-949be7af0a5a)

- 하나의 테이블에 데이터량이 지속적으로 누적됨에 따라 인덱스 조회 성능 저하, SELECT 성능 저하 문제 발생했습니다. 
- 데이터가 "날짜" 기반으로 검색된다는 점에서 "날짜별"(ex : shiproute-20250101. shiproute-20250102) 로 테이블을 분리했습니다.
- 필요한 날짜 테이븖나 조회함에 따라 검색 범위가 좁혀지고 검색 속도가 향상되었습니다.

## 주요 기능

### 로그인 기능
![Image](https://github.com/user-attachments/assets/d79a5ca1-fc32-4937-83b2-43c8aa646326)

### 회원가입 기능
![Image](https://github.com/user-attachments/assets/49d80740-ea2c-4063-ae49-8d45cba005d1)

### 추적 기능
- 선박의 위치가 검은색 마커로 표시되며 파란색 선으로 선박의 10분뒤 위치를 알려줍니다.
- 새로운 위치 정보가 들어올 때 마다 마커의 위치가 변경됩니다.
![Image](https://github.com/user-attachments/assets/088d9dbf-1b16-4827-9f1d-6a69e13e3a95)

### 검색 기능
- 선박번호(MMSI)를 검색창에 입력 후 검색 버튼을 누르면 선박이 위치한 곳으로 화면을 이동시킵니다.
- 마커를 클릭하면 선박에 대한 상세정보를 볼 수 있습니다.
![Image](https://github.com/user-attachments/assets/186752a2-506e-426f-8f8b-899772e7682d)

### 리플레이 기능
- 날짜, 시간을 입력 후 재생 버튼을 눌러 과거의 선박 경로를 확인할 수 있습니다.
![Image](https://github.com/user-attachments/assets/03120119-992e-4074-a9aa-b8ab4cfb21a1)
### 프로젝트 후기

##### 배운점
- TCP 양방향 통신에 대해 배울 수 있었습니다. AIS 서버가 디코딩 서버에 메시지를 보내고 디코딩 서버가 입력 스트림으로 입력받으면 다시 수신확인 메시지를 보내며 응답하는 방식으로 구현했습니다. 통신이 끝나면 socket.close()로 통신을 종료시키도록 했습니다. 이 기능을 구현하면서 서버와 클라이언트간의 상호작용 방식을 배울 수 있었고, 실시간 데이터 송수신에 왜 TCP 방식이 사용되는지 알 수 있었습니다.
- API 명세서를 작성하는 것에 대해 배울 수 있었습니다. github에 push하면서 '혹시나 다른 사람이 내 api를 본다면 한번에 이해할 수 있을까?'라는 의문점을 가지게되었고 인턴 당시 제 사수님께 코드를 보여드리면서 궁금한 점을 여쭈었습니다. 사수님께서는 단순한 api라도 다른 사용자들 입장에서는 어떤 정보를 어떤 방식으로 요청해야하는지, 어떤 정보를 전달받을 수 있을지, 어떤 형식으로 데이터가 넘어오는지 알 수 없기 때문에 명세서는 꼭 작성해야한다고 당부하셨습니다. 저는 구글링과 다른 github api 저장소를 참고하여 api 명세서를 작성하였고 이 과정을 통해 사용자들의 경험을 개선시킬 수 있는 방법에 대해 생각해볼 수 있었습니다.

##### 어려웠던 점
- 메시지를 인코딩, 디코딩하는 과정을 코드로 구현하는 것이 힘들었습니다. 128비트, 424비트라는 긴 길이의 비트를 각 요소별로 나누어 10진수로 변환하는 과정은 많은 시간을 소요했습니다. 특히 타입 5의 메시지가 수신되었을 때 그 직후에 수신되는 메시지까지
이어서 파싱해야한다는 점을 알지 못했었습니다. AIVDM 공식 홈페이지를 통해 이 사실을 알 수 있었고 문제를 해결하였습니다. 
- 오랫동안 정보가 들어오지 않은 마커를 삭제하는 작업이 어려웠습니다. 많은 양의 마커가 웹상에 그려지다 보니 렌더링 성능 저하가 발생했습니다. setTimeout을 활용하여 5분동안 동일한 mmsi가 수신되지 않으면 삭제하는 로직을 구현하였지만
1초당 수신되는 AIS 메시지가 10건에 달했기에, 5분간의 간격으로는 많은 양의 트래픽을 감당하기에는 어려움이 있었습니다. 마커를 그릴 때 feature를 사용하지 않고 canvas layers 등과 같은 방법을 생각했지만 데드라인을 지키지 못해 해결하지 못했습니다. 



