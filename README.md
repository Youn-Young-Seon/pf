# FIREWALL MANAGEMENT SYSTEM

![슬라이드1.jpeg](https://github.com/Youn-Young-Seon/pf/blob/main/%EC%8A%AC%EB%9D%BC%EC%9D%B4%EB%93%9C1.jpeg)

# INTRODUCTION
방화벽 정책 관리 솔루션으로 다수의 방화벽을 통합 관리할 수 있는 기능을 제공합니다.

# 환경
- Ubuntu 20.04
- Nginx
- Apache Tomcat 9

# 기술 스택
- spring boot 2.4.1
- mybatis 1.3.2 
- java 11
- jsp/servlet
- javascript ES5/ES6
- jquery 3.6.0
- react 18.1.0
- typescript 4.9.5
- postgresql 13

# Tools
- VScode
- Intellij
- WebStrom
- DBeaver
- SourceTree

# version / build
- Gradle 6.0.11
- Git / Gitlab
- Jenkins

# 개선 사항
서비스에 추가 또는 개선 되어야 할 항목
1. 담당자가 디바이스 동기화를 수행하면 동기화가 완료 또는 실패 되었는지 새로고침 하기 전에 알 수 없습니다.
2. 담당자가 디바이스 동기화 진행률을 알 수 없고 동기화가 동시에 여러번 요청되면 자원을 과하게 사용합니다. (OOM 발생 가능성)

# 해결 방안
### 1. 담당자가 디바이스 동기화를 수행하면 동기화가 완료 또는 실패 되었는지 새로고침 하기 전에 알 수 없습니다.

담당자(Console server)가 디바이스 동기화를 수행하면 관리 서버(Manager server)에 요청을 비동기로 API call을 합니다. (worker thread로 수행) 
그러면 담당자 UI에서는 call을 수행했다는 response만 받고 요청이 끝나고 같은 화면을 보고 있는 다른 담당자 또한 동기화가 진행이 된 상태인지 아닌지 알 수가 없습니다.

위의 사항을 개선하기 위해 HTTP/1.1 SSE 사용했습니다.

담당자들은 manager server 에서 작업이 완료 되면 console server로 메세지를 전달하고 SSE에 연결되어 있는 담당자들은 동기화가 끝나면 알림과 상태를 공유할 수 있습니다. 
따라서 중복 동기화 요청이 발생하지 않으며, 다른 작업을 진행하다가 동기화가 끝났을 때, 완료된 정보를 확인할 수 있습니다.


아래 링크는 위의 기능을 추가 하기 위해 필요한 기술을 사용해서 만든 프로토타입 입니다.
- [Server 관련 링크](https://github.com/Youn-Young-Seon/sseAndJwt)
- [UI 관련 링크](https://github.com/Youn-Young-Seon/react_sse)

#### 기술 스택
- spring boot / spring webflux
- java 17
- sseEmiter
- react
- jwt

### 2. 담당자가 디바이스 동기화 진행률을 알 수 없고 동기화가 동시에 여러번 요청되면 자원을 과하게 사용합니다. (OOM 발생 가능성)
디바이스의 동기화 처리를 독립적으로 변경함에 따라 담당자(console server)에서 관리 서버(manager server)로 동기화 요청을 동시에 여러번 할 수 있도록 기능을 개선하기로 했습니다.

따라서, 자원 사용을 과하게 할 수 없도록 제한 필요합니다. 또한, console server 에서 방화벽 객체나 정책 등이 DB에 적재되는 진행률을 실시간으로 볼 수 있도록 하는 기능도 필요합니다.

websocket을 이용해서 각 디바이스 동기화 진행률을 담당자에게 실시간으로 표시하고 공유할 수 있습니다.
그리고 java의 concurrent 패키지 api에서 지원하는 기능과 Lock을 이용해서 다수의 요청을 관리합니다.


아래 링크는 위의 기능을 추가 하기 위해 필요한 기술을 사용해서 만든 프로토타입 입니다.
- [Server 관련 링크](https://github.com/Youn-Young-Seon/websocket)
- [UI 관련 링크](https://github.com/Youn-Young-Seon/websocket-client)

#### 기술 스택
- spring boot
- java 17
- websocket
- react
