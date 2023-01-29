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

### 5.2 ChallengeController [코드 확인](https://github.com/dbgys1127/wiselife/blob/main/server/wiselife/src/main/java/be/wiselife/challenge/controller/ChallengeController.java) 

- 가입된 사용자의 정보 확인 [코드 확인](https://github.com/dbgys1127/wiselife/blob/main/server/wiselife/src/main/java/be/wiselife/aop/LoginAspect.java) 
  - 모든 페이지 접근 마다 필요한 작업으로 중복방지를 위해 AOP로 관심사를 분리 
- 인증사진 S3 등록과 S3에 저장된 주소 DB 저장 요청

### 5.3 ChallengeService 
- 인증사진 등록과 관련된 로직 처리를 ImageService에 요청함

### 5.4 ImageService [코드 확인](https://github.com/dbgys1127/wiselife/blob/main/server/wiselife/src/main/java/be/wiselife/image/service/ImageService.java) 

 1. 인증 가능 시간인가? 
 <details>
 <summary><b>코드 펼치기</b></summary>
 <div markdown="1">
 
    ```java
    public Challenge patchChallengeCertImage(Challenge challenge, Member loginMember) {
        log.info("patchReviewImage tx start");

        //인증가능 시간인지 검증
        if(!isAuthAvailableTime(challenge))
           throw new BusinessLogicException(ExceptionCode.NOT_CERTIFICATION_AVAILABLE_TIME);
    ```
 </div>
 </details>
    - 인증 사진 등록하는 시간이 챌린지 인증시간이 아니면, 예외 발생

 2. 챌린지 참여회원인가?