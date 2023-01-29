# :alarm_clock: 슬기로운 생활
 - 웹사이트 : [바로가기](https://wiselife.click/)
 <br><br>
신년마다 사람들은 그해에 이루고자 하는 목표를 생각합니다.
<br>하지만 그 목표들을 이루지 못 하고 내년으로 미뤄지게 되는 경우가 다반수죠.
<br>마라톤에도 페이스메이커가 있듯 목표달성을 도와주는 서비스가 필요하다면 슬기로운 생활과 함께하세요.
<img src ="https://ifh.cc/g/Rag38b.jpg" width="100%" height="250"/>

<br>

## 1. 제작 기간 & 참여인원

---

    개발 기간 2022.11.8 - 2022.12.7 (30일)
    백엔드 3명, 프론트 엔드 3명 참가

<br>

## 2. 사용 기술

---

### Backend

<img src="https://img.shields.io/badge/Spring Boot-6DB33F?style=for-the-badge&logo=Spring Boot&logoColor=white"> <img src="https://img.shields.io/badge/Spring Data JPA-6DB33F?style=for-the-badge&logo=Spring Boot&logoColor=white">
<img src="https://img.shields.io/badge/Spring Security-6DB33F?style=for-the-badge&logo=Spring Security&logoColor=white"> <img src="https://img.shields.io/badge/Gradle-02303A?style=for-the-badge&logo=Gradle&logoColor=white">
<img src="https://img.shields.io/badge/QueryDsl-4479A1?style=for-the-badge&logo=Querydsl&logoColor=white">
<img src="https://img.shields.io/badge/mysql-4479A1?style=for-the-badge&logo=mysql&logoColor=white"> <img src="https://img.shields.io/badge/JAVA-007396?style=for-the-badge&logo=java&logoColor=white">
<img src="https://img.shields.io/badge/Auth2-EB5424?style=for-the-badge&logo=Auth0&logoColor=white">
<br>

## Collaboration Tools

<img src="https://img.shields.io/badge/GitHub-181717?style=for-the-badge&logo=GitHub&logoColor=white"> <img src="https://img.shields.io/badge/Discord-5865F2?style=for-the-badge&logo=Discord&logoColor=white">

<br>
<br>

## 3. ERD 설계
---
<img src="img/wiselifeerd.png" width="100%" height="auto"></img><br/>
<br>

## 4. AWS 구조

---
[![aws-arc.png](https://i.postimg.cc/Hssjtfd3/aws-arc.png)](https://postimg.cc/bdWzyVG2)

<br>

## 5. 핵심 기능
---
이 서비스의 핵심 기능은 사용자가 챌린지에 정의된 인증 기준에 부합되게 인증 사진을 올리면, 그예 따른 성공률을 계산하여, 챌린지 종료시 사용자에게 보상을 해주는 동기부여 서비스입니다. 

### 5.1 사용자 흐름
<img src ="img/wiselife-big-flow.001.png" width="100%" height="auto"/>

<br>

### 5.2 ChallengeController [코드링크-ChallengeController](https://github.com/dbgys1127/wiselife/blob/main/server/wiselife/src/main/java/be/wiselife/challenge/controller/ChallengeController.java) 

- 가입된 사용자의 정보 확인 [코드링크-LoginAspect](https://github.com/dbgys1127/wiselife/blob/main/server/wiselife/src/main/java/be/wiselife/aop/LoginAspect.java) 
  - 모든 페이지 접근 마다 필요한 작업으로 `중복방지를 위해 AOP로 관심사를 분리` 
- 인증사진 S3 등록과 S3에 저장된 주소 DB 저장 요청

### 5.3 ChallengeService 
- 인증사진 등록과 관련된 로직 처리를 ImageService에 요청했습니다.

### 5.4 ImageService [코드링크-ImageService](https://github.com/dbgys1127/wiselife/blob/main/server/wiselife/src/main/java/be/wiselife/image/service/ImageService.java) 

 1. 인증 가능 시간인가? <details><summary><b>코드 펼치기</b></summary><img src ="img/인증가능시간확인.001.png" width="100%" height="auto"/></details>
    - 사용자가 사진 등록하는 시간이 챌린지 인증시간이 아니면, 예외 발생

 2. 챌린지 참여회원인가?<details><summary><b>코드 펼치기</b></summary><img src ="img/참여회원.001.png" width="100%" height="auto"/></details>
    - 사용자가 챌린지에 참여하고 있는 회원이 아니라면, 챌린지에 참여를 먼저 해야한다는 예외 발생

 3. 같은 시간에 등록한 사진이 존재하는가?<details><summary><b>코드 펼치기</b></summary><img src ="img/수정여부.001.png" width="100%" height="auto"/><br><img src ="img/수정여부확인후.001.png" width="100%" height="auto"/></details>
    - `고민` 인증사진을 수정할때, 따로 URI를 둬야할까? 
    
    - `해결책` 컨트롤러에서 인증사진 처리 메서드를 `patch`MemberCertification로 둔 것처럼 요청을 처리하는 비즈니스 로직 부분에서 동일한 시간에 사진이 있다면 수정, 없다면 신규등록되게 구성하였습니다.

4. 오늘 의무 인증 횟수를 다 채웠는가?<details><summary><b>코드 펼치기</b></summary><img src ="img/인증횟수.001.png" width="100%" height="auto"/></details>
    - 챌린지에서 명시된 금일 인증횟수를 초과했을때, 불필요한 데이터가 쌓이지 않게 예외처리가 필요하다 생각했습니다.

5. 인증 사진이 성공적으로 등록된 후 처리되는 작업
    - 챌린지 참여 회원이 그날 의무 인증횟수를 만족하면 성공일로 간주하고, 성공률을 계산하였습니다. (ex. 4일차에 3일 성공하면 75%)<details><summary><b>코드 펼치기</b></summary><img src ="img/성공인정.001.png" width="100%" height="auto"/></details>
    - 인증할때 마다 회원 등급 변화 <details><summary><b>코드 펼치기</b></summary><img src ="img/등급변화.001.png" width="100%" height="auto"/></details>