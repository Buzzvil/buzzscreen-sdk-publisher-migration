# Communication Utils Guide
- 마이그레이션 SDK 에서는 M앱과 L앱 사이의 통신을 위한 유틸리티를 제공합니다.
- 총 3종류의 유틸리티 클래스를 제공합니다.
    - 공유되는 key-value 쌍을 저장해두고 이용하려는 경우 : [DataStorage](COMMUNICATION-UTILS.md#datastorage)
    - 단방향 이벤트 전달이 필요한 경우 : [EventHandler](COMMUNICATION-UTILS.md#eventhandler)
    - 완전한 서버-클라이언트 구조가 필요한 경우 : [RequestHandler](COMMUNICATION-UTILS.md#requesthandler)

## DataStorage

- 두 앱 사이에 공유되는 데이터가 필요한 경우 사용합니다.
- 간단한 key-value 쌍으로 데이터를 관리할 수 있습니다.
- `MigrationXXX.getDataStorage()` 를 통해 DataStorage instance를 가져올 수 있습니다.

> 마이그레이션 SDK에서는 L앱의 잠금화면이 활성화 되어 있는지 여부를 확인할 때 사용하고 있습니다. L앱에서 잠금화면이 활성화 되거나 비활성화 될 때마다 DataStorage에 값을 업데이트 시키며, M앱에서는 DataStorage로부터 이 값을 읽어서 관련 로직을 처리합니다.

#### Methods
- `void put(String key, String value)` : key 에 매핑되는 value 를 저장합니다.
- `String get(String key)` : key 에 매핑된 value 를 Synchronous 하게 가져와서 리턴합니다. 
- `void getAsync(String key, AsyncQueryListener listener)` : key 에 매핑된 value 를 Asynchronous 하게 가져와서 AsyncQueryListener 에 보냅니다.
    - AsyncQueryListener : key에 대한 쿼리가 완료되면 onQueryComplete 메소드가 호출되며 파라미터로 value 가 전달됩니다.
    ```java
    public interface AsyncQueryListener {
        void onQueryComplete(String value);
    }
    ```

#### 주의사항
- 각 앱은 put 을 호출하면 자신의 저장소에 저장하며, get 을 호출하면 자신 -> 상대편 앱 순서대로 성공할 때까지 찾아서 리턴합니다.
- 따라서 두 앱 모두에서 put 을 통해 서로 다른 값을 넣으면 저장소가 분리되어 있으므로 공유가 되지 않습니다. put 은 한쪽에서만 호출해야 합니다.
- DataStorage 는 ContentProvider 를 사용하고 있습니다. ContentProvider 특성상 소요 시간이 길어질 수 있으므로 너무 빈번하게 호출하지 않는게 좋습니다.

#### Code Example
```java
MigrationXXX.getDataStorage().put("SHARED_CONFIG_KEY", "config_value");

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

- Sender 로부터 Receiver 에게로 한쪽 방향으로만 이벤트를 전달하는 경우 사용합니다.
- 이벤트에 추가적으로 넣을 정보는 Bundle 로 전달 가능합니다.
- `MigrationXXX.getEventHandler()` 를 통해 EventHandler instance를 가져올 수 있습니다.

> 마이그레이션 SDK에서는 M앱에서 L앱의 잠금화면을 비활성화 시킬 때 사용하고 있습니다(MigrationHost의 requestDeactivation()). L앱이 Receiver 로서 잠금화면을 비활성화시키는 로직을 구현해서 이벤트로 등록해 두고, M앱이 Sender 로서 해당 이벤트를 전송합니다.

#### Methods
##### Sender
- `void post(String eventName)` : 특정 eventName 으로 정의된 이벤트를 보냅니다.
- `void post(String eventName, Bundle extras)` : Bundle 형태의 데이터를 담아서 특정 eventName으로 정의된 이벤트를 보냅니다.
##### Receiver
- `void registerEventListener(String eventName, OnEventListener listener)` : 특정 이벤트를 받았을 때의 로직을 OnEventListener를 통해 구현해서 eventName과 매핑해 등록합니다.
    - OnEventListener : 이벤트를 받았을 때 onEvent 메소드가 호출되며 파라미터로 post 에서 넣은 extras 가 전달됩니다(없을 경우 null 이 아닌 빈 Bundle 전달).
    ```java
    public interface OnEventListener {
        void onEvent(Bundle extras);
    }
    ```
- `void unregisterEventListener(String eventName)` : eventName으로 등록되어 있는 OnEventListener 를 해제합니다.

#### 주의사항
- Multiprocess 를 사용하는 경우

#### Code Example
```java
// Sender
Bundle eventData = new Bundle();
eventData.putString("extra_info", "extra_value");
MigrationXXX.getEventHandler().post("SAMPLE_EVENT", eventData);

// Receiver
MigrationXXX.getEventHandler().registerEventListener("SAMPLE_EVENT", new EventHandler.OnEventListener() {
    @Override
    public void onEvent(Bundle extras) {
        Log.d(TAG, "onReceive SAMPLE_EVENT");
        String extraInfo = extras.getString("extra_info"); // "extra_value" returned
    }
});

```


## RequestHandler

- 서버-클라이언트 구조와 같이 유기적으로 요청과 이에 대한 응답 처리가 필요한 경우 사용합니다.
- 요청과 응답 시 주고받는 데이터는 Bundle 을 이용합니다.
- `MigrationXXX.getRequestHandler()` 를 통해 RequestHandler instance를 가져올 수 있습니다.

> 마이그레이션 SDK에서는 L앱에서 M앱의 버즈스크린 사용 정보를 가지고 올 때 사용하고 있습니다(MigrationClient의 checkAvailability()). L앱이 클라이언트로서 M앱에 버즈스크린 사용 정보를 요청하며, M앱은 서버로서 이 요청을 받으면 버즈스크린 사용 정보를 담아 응답합니다. L앱은 이 응답을 통해 잠금화면을 활성화하는 등의 로직을 처리합니다.

#### Methods
##### Client
- `void request(int requestCode, Bundle params, Request.OnResponseListener listener)`
##### Server
- `void registerResponder(int requestCode, MsgRequestHandler.Responder responder)`

#### 주의사항
- Multiprocess 를 사용하는 경우

#### Code Example
```java
// Client
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

// Server
MigrationXXX.getRequestHandler().registerResponder(1000, new MsgRequestHandler.Responder() {
    @Override
    public Bundle respond(Bundle parameters) {
        Bundle response = new Bundle();
        response.putString("sample_key", "sample_value");
        return response;
    }
});
```
