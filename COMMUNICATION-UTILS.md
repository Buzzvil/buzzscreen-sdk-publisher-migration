# Communication Utils Guide
- 마이그레이션 SDK에서는 M앱과 L앱 사이의 통신을 위한 유틸리티를 제공합니다.
- 총 3종류의 유틸리티 클래스를 제공합니다.
    - 공유되는 key-value 쌍을 저장하려는 경우 : [DataStorage]()
    - 단방향 이벤트 전달이 필요한 경우 : [EventHandler]()
    - 완전한 서버-클라이언트 구조가 필요한 경우 : [RequestHandler]()

## DataStorage
### 주의사항
- 양쪽 모두가 put 을 호출하게 되면 값이 의도한 대로 업데이트 되지 않습니다.

## EventHandler
### 주의사항

## RequestHandler
### 주의사항
