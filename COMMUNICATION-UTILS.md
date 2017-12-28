# Communication Utils Guide
- 마이그레이션 SDK에서는 M앱과 L앱 사이의 통신을 위한 유틸리티를 제공합니다.
- 총 3종류의 유틸리티 클래스를 제공합니다.
    - 공유되는 key-value 쌍을 저장해두고 이용하려는 경우 : [DataStorage]()
    - 단방향 이벤트 전달이 필요한 경우 : [EventHandler]()
    - 완전한 서버-클라이언트 구조가 필요한 경우 : [RequestHandler]()

## DataStorage

두 앱 사이에 공유되는 데이터를 저장해야 할 때 사용합니다. 간단한 key-value 쌍으로 데이터를 관리할 수 있습니다.

> MigrationXXX.getDataStorage() 를 통해 DataStorage instance를 가져올 수 있습니다.

### Methods

- `void put(String key, String value)` : key 에 매핑되는 value 를 저장합니다.
- `String get(String key)` : key 에 매핑된 value 를 Synchronous 하게 가져옵니다. 
- `void getAsync(String key, AsyncQueryListener listener)` : key 에 매핑된 value 를 Asynchronous 하게 

### 주의사항
- DataStorage는 ContentProvider를 사용하고 있습니다. ContentProvider 특성상 상황에 따라 소요 시간이 길어질 수 있으므로 너무 빈번하게 호출하지 않는게 좋습니다.
- 양쪽 모두가 put 을 호출하게 되면 값이 의도한 대로 업데이트 되지 않습니다.

### Example
```java
MigrationXXX.getDataStorage().put("SHARED_CONFIG_KEY", "value");

// Synchronous
String value = MigrationXXX.getDataStorage().get("SHARED_CONFIG_KEY");
// Asynchronous
MigrationXXX.getDataStorage().getAsync("SHARED_CONFIG_KEY", new DataStorage.AsyncQueryListener() {
    @Override
    public void onQueryComplete(String value) {
    }
});
```

## EventHandler

> MigrationXXX.getEventHandler() 를 통해 EventHandler instance를 가져올 수 있습니다.

### Methods
- `void post(String eventName)`
- `void post(String eventName, Bundle extras)`
- `void registerEventListener(String key, OnEventListener listener)`
- `void unregisterEventListener(String key)`

### 주의사항

### Example
```java
MigrationXXX.getEventHandler().registerEventListener("SAMPLE_EVENT", new EventHandler.OnEventListener() {
    @Override
    public void onEvent(Bundle extras) {
        Log.d(TAG, "onReceive SAMPLE_EVENT");
    }
});

MigrationXXX.getEventHandler().post("SAMPLE_EVENT");
```

## RequestHandler

> MigrationXXX.getRequestHandler() 를 통해 RequestHandler instance를 가져올 수 있습니다.

### Methods
- `void request(int requestCode, Bundle params, Request.OnResponseListener listener)`
- `void registerResponder(int requestCode, MsgRequestHandler.Responder responder)`

### 주의사항

### Example
```java
MigrationXXX.getRequestHandler().request(1000, null, new Request.OnResponseListener() {
    @Override
    public void onResponse(Bundle response) {
        String value = response.getString("sample_key");
        Log.d(TAG, "onResponse - " + value);
    }

    @Override
    public void onFail(Request.FailReason failReason) {
    }
});

MigrationXXX.getRequestHandler().registerResponder(1000, new MsgRequestHandler.Responder() {
    @Override
    public Bundle respond(Bundle parameters) {
        Bundle response = new Bundle();
        response.putString("sample_key", "sample_value");
        return response;
    }
});
```
