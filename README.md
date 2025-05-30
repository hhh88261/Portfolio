# 실시간 선박 추척 GIS 어플리케이션 

## 목차
1. 기술 스택    
2. 프로젝트 개요    
3. 프로젝트 소개  
4. 아키텍처   
5. 주요 기능   
6. 리팩토링 및 성능개선 

## 기술 스택
<img src="https://img.shields.io/badge/vue.js-41B883?style=for-the-badge&logo=vue.js&logoColor=white"> : 수백개의 마커가 웹상에 표시되야 했기에 렌더링이 빠른 vue를 선택  
<img src="https://img.shields.io/badge/java-007396?style=for-the-badge&logo=OpenJDK&logoColor=white"> : java.net으로 소켓 통신을 구현하고 maven에서 AIS 메시지를 파싱하는 의존성을 사용하기 위해 채택  
<img src="https://img.shields.io/badge/springboot-6DB33F?style=for-the-badge&logo=springboot&logoColor=white"> : Spring mvc로 api를 구현하기 위해 채택  
![Oracle](https://img.shields.io/badge/Oracle-F80000?style=for-the-badge&logo=oracle&logoColor=white) 

## 프로젝트 개요
- 실시간 선박 추적 프로그램
- 프로젝트 기간 : 2024.09 ~ 2024.12
- 프로젝트 인원 : 1명

## 프로젝트 소개
- 이 프로그램은 AIS(자동식별시스템)를 활용하여 해양에서 운항하고 있는 선박을 추적하거나 항로를 확인할 수 있고 선박의 국적, 속도, 방향 등과 같은 제원정보도 모니터링할 수 있다.

> 백엔드  
> 송신 서버    
https://github.com/hhh88261/AIS-Custom-Server  
> Parsing 서버(파싱 + 수신)      
https://github.com/hhh88261/Marine-vessel-information-Server  
> Spring Boot (REST)  
https://github.com/hhh88261/Vessel-location-information-coastal-AIS-API  

> 프론트엔드(Vue)     
https://github.com/hhh88261/Marine-vessel-information-App


## 프로젝트 아키텍처
![Image](https://github.com/user-attachments/assets/9a21127b-2a39-4f6c-b945-d9cda011596c)
#### 구조
- AIS 신호 송신서버, 수신서버, API 서버, 클라이언트로 구성된다.  
- 사용자 요청은 REST API 기반으로 처리한다.
- JWT 토큰으로 로그인 인증을 관리한다. 
  
#### 흐름
1. 사용자 로그인
2. 파싱서버에 요청 전송
3. 송신서버에서 원시 AIS 메시지를 생성하여 파싱서버로 전송  
4. 파싱서버에서 TCP 소켓을 사용해 데이터를 수신 
5. 데이터 가공 및 DB 저장 후 Web Socket을 통해 클라이언트에게 메시지를 송신  
6. 클라이언트에서 수신받은 데이터를 바탕으로 화면에 선박 위치를 표시  
   
#### 접근 방식
1. 메시지 처리, 송신, 수신 프론트엔드를 분리하여 확장성을 확보
2. 표준 파싱 라이브러리를 사용하여 실제 AIS 송신서버와 연동 대비

#### 기술 사용 근거  
- 송신서버와 파싱서버간 통신에서는 TCP가 WebSocket보다 전송지연이 적어 고속 전송에 적합하기 때문에 사용했다.  
- 브라우저와 서버 간 지속적인 통신을 해야하기 때문에 WebSocket 방식을 사용했다.
- 인증정보 탈취 위험에 대비하기 위해 JWT를 도입했다. Refresh Token과 Access Token을 분리하여 보안성을 강화했다.  

#### 고려사항   
- Kafka 도입 : 현재는 단순한 수신->저장->시각화 형태이기 때문에 Kafka와 같은 대규모 분산 스트림 처리 불필요,
  다만 향후에 복잡한 기능이 추가된다면 도입할 필요가 있음  
  
### ERD
![Image](https://github.com/user-attachments/assets/0eb312bf-c9cd-45cb-acd3-949be7af0a5a)

## 주요 기능

### 로그인 기능  
- 아이디와 비밀번호를 입력해 로그인한다.  
- Accounts 테이블에 유저 정보가 있다면 Refresh Token과 Access Token을 발급받는다.  
- 로그인에 성공하면 /Main 으로 이동한다.   
![Image](https://github.com/user-attachments/assets/d79a5ca1-fc32-4937-83b2-43c8aa646326)


### 회원가입 기능  
- 이름, 이메일, 비밀번호를 입력하여 회원가입을 할 수 있다.  
- 유저 정보는 Accounts 테이블에 저장된다.  
- '회원가입' 버튼을 클릭하면 로그인 화면으로 돌아간다.  
![Image](https://github.com/user-attachments/assets/49d80740-ea2c-4063-ae49-8d45cba005d1)

### 추적 기능
- 선박의 위치가 검은색 마커로 표시되며 파란색 선으로 선박의 10분뒤 위치를 예측한다.
- 새로운 위치 정보가 들어올 때 마다 마커의 위치가 변경된다.
![Image](https://github.com/user-attachments/assets/088d9dbf-1b16-4827-9f1d-6a69e13e3a95)

### 검색 기능
- 선박번호(MMSI)를 검색창에 입력 후 검색 버튼을 누르면 선박이 위치한 곳으로 화면을 이동시킨다.
- 마커를 클릭하면 선박에 대한 상세정보를 볼 수 있다.
![Image](https://github.com/user-attachments/assets/186752a2-506e-426f-8f8b-899772e7682d)

### 리플레이 기능
- 날짜, 시간을 입력 후 재생 버튼을 눌러 과거의 선박 경로를 확인할 수 있다.
![Image](https://github.com/user-attachments/assets/03120119-992e-4074-a9aa-b8ab4cfb21a1)

## 인덱스 / 파티셔닝  
가설 :   
현재 과거항적을 저장하는 테이블이 있다. 
하지만 다수의 클라이언트가 한 테이블을 반복적으로 조회하거나 시간이 지나 테이블이 크기가 커질수록 조회 성능이 저하됬다. 인덱스와 파티셔닝을 활용하면 탐색비용을 줄이고 성능 저하 현상을 개선할 수 있을 것이다.

Case1 : 인덱스 미수행, 파티셔닝 미수행  
Case2 : 인덱스, 날짜별 Range 파티셔닝 수행  
Case3 : 인덱스, 테이블 분할 수행  

결과 정리 :  
![Image](https://github.com/user-attachments/assets/84413299-c0e2-485c-99bb-bd81a12ab1a8)

결과 분석 : 
물리적으로 테이블을 분리하고 인덱스를 설정하여 평균 응답시간이 28ms에서 5.2ms로 줄었다.

Case1 : Full Scan이 발생했다.  
Case2 : Start Time과 End Time 간격이 커질 수록 조회 범위가 커졌고 중복되는 날짜, MMSI가 많아 이중 인덱스 스캔이 발생하여 효과적이지 않았다.  
Case3 : 예상보다 성능향상이 크게 이루어졌다. 날짜별로 테이블을 나누어 테이블 크기가 감소했고 그에 따라 불필요한 데이터가 줄어들어 빠른 접근이 가능했기 때문이다. 또한 파티션 프루닝을 위한 내부 처리를 하지 않고 단일 테이블 하나만 접근함으로써 결과가 즉각적으로 나올 수 있었던 것으로 보인다.  

단점 :   
날짜가 지날수록 테이블 수가 늘어남에 따라 디스크에 부하를 줄 수 있다. 1년 간격으로 테이블을 삭제하는 스케줄러를 추가하여 관리하는 방법을 고려할 수 있다.


## BIO vs NIO
가설 :   
현재 디코딩 서버의 데이터 수신 방식은 Polling 방식이다. 
하지만 연결이 늘어나 처리량이 늘어날수록 성능이 저하되는 문제가 발생했다.    
While 반복문으로 데이터를 감시하던 방식을 이벤트 기반의 방식을 사용하면 이벤트가 발생할 때에만 자원을 사용하므로 이러한 문제점을 개선할 수 있을 것이다.  

Case1 : 초당 1개의 데이터 5분 간 수신  
Case2 : 초당 5개의 데이터 5분 간 수신  
Case3 : 초당 10개의 데이터 5분 간 수신    

결과 정리 :   
![Image](https://github.com/user-attachments/assets/f44090e4-1ed3-40df-883d-038eeb567284)  

결과 분석 :   
전체적으로 데이터 양과 연결 수가 늘어날수록 NIO의 효율성이 높아지는 경향을 보였다.  
소량의 데이터를 처리할 때는 기존 블로킹 구조가 단순하여 처리가 더 빨랐다.   
하지만 클라이언트 연결이 늘어나 스레드가 증가할수록 다수,고빈도 이벤트 처리에 최적화된 NIO가 더 효율적으로 작동했다.    

고려사항 :  
수신량이 제한된 현재 프로젝트 구조에서는 기존 방식을 유지하는 것이 유리하다.    
다만 실제 AIS 송신 서버와 연결하여 수천개의 데이터를 수신해야 한다면 Java NIO 방식을 채택하는 것이 효율적일 것이다.  

분석도구 : Apache Jmeter    



