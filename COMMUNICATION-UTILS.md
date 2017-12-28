# Communication Utils Guide
- 마이그레이션 SDK에서는 M앱과 L앱 사이의 통신을 위한 유틸리티를 제공합니다.
- 총 3종류의 유틸리티 클래스를 제공합니다.
    - 공유되는 key-value 쌍을 저장해두고 이용하려는 경우 : [DataStorage]()
    - 단방향 이벤트 전달이 필요한 경우 : [EventHandler]()
    - 완전한 서버-클라이언트 구조가 필요한 경우 : [RequestHandler]()

## DataStorage
### 주의사항
- 양쪽 모두가 put 을 호출하게 되면 값이 의도한 대로 업데이트 되지 않습니다.

```java
MigrationX.getDataStorage().put("SHARED_CONFIG_KEY", "value");

// Synchronous
String value = MigrationX.getDataStorage().get("SHARED_CONFIG_KEY");
// Asynchronous
MigrationX.getDataStorage().getAsync("SHARED_CONFIG_KEY", new DataStorage.AsyncQueryListener() {
    @Override
    public void onQueryComplete(String value) {
    }
});
```

## EventHandler
### 주의사항

```java
MigrationX.getEventHandler().registerEventListener("SAMPLE_EVENT", new EventHandler.OnEventListener() {
    @Override
    public void onEvent(Bundle extras) {
        Log.d(TAG, "onReceive SAMPLE_EVENT");
    }
});

MigrationX.getEventHandler().post("SAMPLE_EVENT");
```

## RequestHandler
### 주의사항

```java
MigrationX.getRequestHandler().request(1000, null, new Request.OnResponseListener() {
    @Override
    public void onResponse(Bundle response) {
        String value = response.getString("sample_key");
        Log.d(TAG, "onResponse - " + value);
    }

    @Override
    public void onFail(Request.FailReason failReason) {
    }
});

MigrationX.getRequestHandler().registerResponder(1000, new MsgRequestHandler.Responder() {
    @Override
    public Bundle respond(Bundle parameters) {
        Bundle response = new Bundle();
        response.putString("sample_key", "sample_value");
        return response;
    }
});
```
