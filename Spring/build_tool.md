### 1. Maven

빌드 도구.

- 정형화된 프로젝트 폴더 구조 존재 (pom.xml 파일을 통해 컨트롤)
- 의존성 네트워크로 필요한 모듈을 자동으로 다운로드.
- 프로젝트 템플릿 배포
- pom.xml(Project Object Model) : 프로젝트 정보, 구조 설명. Maven 단계 별 필요 작업 명시.
    - groupId : 회사,본부,단체 의미하는 값, 패키지형식으로 작성.
    - artifactId : 패키지를 감싸는 프로젝트 느낌
    - package : 별 표시 없으면 groupId와 동일하게 생성됨.

```
groupId:net.madvirus
artifactId:sample
sample
├─src
│   ├─main
│   │  └─java
│   │      └─net
│   │          └─madvirus
│   │              └─App.java
│   ├─test
│       └─java
│           └─net
│               └─madvirus
│                   └─AppTest.java
└─pom.xml
```

- 라이프사이클
    - **complie** : src/java 밑의 모든 자바소스를 컴파일해서 target/classes로 복사
    - **test** : target/test-classes에 있는 테스트케이스의 단위테스트를 진행한다. 결과는 target/surefie-reports에 생성(기본설정은 단위테스트가 실패하며 빌드 실패로 간주함)
    - **package** : target디렉토리 하위에 **jar, war, ear등 패키지파일을 생성**한다. 이름은 pom.xml 태그의 값을 사용한다.
    - **install** : 로컬 레포지토리(USER_HOME/.m2)로 packaging한 파일을 배포한다.
    - **deploy** : 패키징한 파일을 원격 저장소에 배포(nexus혹은 maven central 저장소)
    
    ---
    
    [](https://twofootdog.github.io/Spring-Maven-%EA%B0%9C%EB%85%90-%EC%A0%95%EB%A6%AC/)
    
    ['Maven 라이프 사이클' 태그의 글 목록](https://javacan.tistory.com/tag/Maven%20%EB%9D%BC%EC%9D%B4%ED%94%84%20%EC%82%AC%EC%9D%B4%ED%81%B4)
    

### 2. Jenkins

지속적 통합 (Continuous Integration)

개발팀이 작업한 코드를 주기적으로 자주 통합한다.

**2-1. 특징**

- maven install 등을 jenkins에서 gui 이용할 수 있음.
- Build 자동화
- 빌드 파이프라인 구성 : 여러 모듈들을 순서대로 빌드해야할 때 ( ex. 도메인 -> 서비스 -> UI ) 유용하게 사용

**2-2. 키워드**

- **Github Webhook(Jenkins Trigger)**
    
    Github Repository에 Webhook을 걸어서 Jenkins와 연동하면, Github에서 push하거나 event가 일어났을 때, Jenkins에서 빌드를 자동화해줄 수 있음.
    
    방법 : 
    
    1. Github Repository → Setting → Webhook → Generate
        1. payload : http://<jenkins IP>:<port>**/github-webhook/** 
        2. content type : application/json
        
        ※ <jenkins IP>에서 로컬 호스트를 사용할 때는 ngrok 등을 통해 포트포워딩을 이용할 것.
        
    2. Jenkins 구성 → 소스코드 → Git URI 입력 → Credentials 추가
        1. Credential 방법1 : Secret text
            1. Github Personal Access Token 사용
        2. Credential 방법2 : Username with Password
            1. Github username, password 입력
    3. 빌드유발 → GitHub hook trigger for GITScm polling 체크 

- **Slack Notification**
    
    Jenkins 빌드 결과를 Slack으로 받기
    
    1. Slack에서 워크스페이스 선택 → App → Jenkins CI 설치 → 채널 선택
    2. Jenkins에서 Slack Notification Plugin 설치 → Jenkins 관리 → 시스템 구성
        1. Workspace : 알림을 받을 슬랙 워크스페이스 입력
        2. Credential : secret text 선택, 1에서 얻은 토큰 복붙.
        3. 채널 이름 입력.
