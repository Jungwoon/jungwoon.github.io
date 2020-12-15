---
layout: post
title: "Realm 훑어보기"
image: '/assets/img/'
description: '요즘 핫한 Realm 훑어보기'
tags:
- Database
- Android
- Realm
categories:
- Android
---

다음 내용들은 Realm 홈페이지의 내용들을 정리한 내용입니다.
[Realm 페이지](https://realm.io/kr/)


## Realm
Realm을 사용하기 전에 먼저 Realm을 초기화하여야 합니다.

```java
Realm.init(context);
```

Realm을 초기화하기 가장 좋은 곳은 어플리케이션 서브 클래스의 onCreate() 입니다.

```java
public class MyApplication extends Application {
  @Override
  public void onCreate() {
    super.onCreate();
    Realm.init(this);
  }
}
```

### Realm 설정하기
Realm을 어떻게 생성하는지 모든 측면을 제어하기 위해 RealmConfiguration 객체를 사용합니다.

```java
// RealmConfiguration은 빌더 패턴에 의해 생성됩니다.
// Realm 파일은 Context.getFilesDir()에 위치하면 이름은 "myrealm.realm"입니다.
RealmConfiguration config = new RealmConfiguration.Builder()
  .name("myrealm.realm")
  .encryptionKey(getKey())
  .schemaVersion(42)
  .modules(new MySchemaModule())
  .migration(new MyMigration())
  .build();
// 설정을 사용합니다.
Realm realm = Realm.getInstance(config);
```

### Realm의 절대 경로 얻기

```java
Realm.getPath();
```

### 기본 RealmConfiguration

```java
public class MyApplication extends Application {
  @Override
  public void onCreate() {
    super.onCreate();
    // Context.getFilesDir()에 "default.realm"란 이름으로 Realm 파일이 위치한다
    Realm.init(this);
    RealmConfiguration config = new RealmConfiguration.Builder().build();
    Realm.setDefaultConfiguration(config);
  }
}

public class MyActivity extends Activity {
  @Override
  protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    Realm realm = Realm.getDefaultInstance();
    // ... 무엇을 합니다 ...
    realm.close();
  }
}
```


### In-memory Realm
아래와 같이 설정하면 디스크에 저장하지 않는 인 메모리 Realm이 생성됩니다. 인 메모리 Realm은 메모리가 부족하면 여전히 디스크를 이용합니다. 하지만 모든 파일들은 메모리 상에서 생성되고 Realm이 닫히면 삭제됩니다.

```java
RealmConfiguration myConfig = new RealmConfiguration.Builder()
    .name("myrealm.realm")
    .inMemory()
    .build();
```


### Realm 객체 닫기
Realm은 Native Memory Release와 File Descriptor를 다루기 위해 Closeable을 구현합니다.

Realm 인스턴스는 레퍼런스 카운티잉 되기 때문 getInstance()를 두 번 호출하면 사용이 끝난 후에도 close()를 두 번 해주어야 합니다.

UI Thread

```java
@Override
protected void onDestroy() {
	super.onDestroy();
	realm.close();
}
```


Thread

```java
// Realm 인스턴스와 비 Looper 스레드를 실행합니다.
Thread thread = new Thread(new Runnable() {
    @Override
    public void run() {
        Realm realm = Realm.getDefaultInstance();
        try {
            // ... Realm 인스턴스를 사용합니다 ...
        } finally {
            realm.close();
        }
    }
});

thread.start();
```

## 모델

### 생성방법
모델은 RealmObject 에서 상속을 받아 생성합니다.

```java
public class User extends RealmObject {

    private String          name;
    private int             age;

    @Ignore
    private int             sessionId;

    // IDE에 의해 생성된 표준 게터와 세터들...
    public String getName() { return name; }
    public void   setName(String name) { this.name = name; }
    public int    getAge() { return age; }
    public void   setAge(int age) { this.age = age; }
    public int    getSessionId() { return sessionId; }
    public void   setSessionId(int sessionId) { this.sessionId = sessionId; }
}
```

### @Required
null 값을 허용하지 않음, 기본형과 RealmList 형에 대한 필드들은 암묵적으로 Required로 취급됩니다. RealmObject 형의 필드들은 항상 null이 가능

### @Ignore
필드가 디스크에 저장되지 않도록 하는 어노테이션으로, 보통 쓰지 않을 데이터 필드를 다루는 특별 케이스를 만들때 유용합니다.

### 자동 갱신 객체
Realm 객체와 RealmResults 는 기반 데이터에 의해 라이브로 자동 갱신되는 뷰입니다. 즉 결과를 매번 가져오지 않아도 되게 됩니다.

```java
realm.executeTransaction(new Realm.Transaction() {
    @Override
    public void execute(Realm realm) {
        Dog myDog = realm.createObject(Dog.class);
        myDog.setName("Fido");
        myDog.setAge(1);
    }
});
Dog myDog = realm.where(Dog.class).equalTo("age", 1).findFirst();

realm.executeTransaction(new Realm.Transaction() {
    @Override
    public void execute(Realm realm) {
        Dog myPuppy = realm.where(Dog.class).equalTo("age", 1).findFirst();
        myPuppy.setAge(2);
    }
});
```

### @Index
해당 필드에 검색 색인을 추가하여, 데이터의 크기는 커지지만, 질의 처리를 빠르게 할 수 있습니다.

### @PrimaryKey
필드를 기본키로 다루기 위해서, 사용해야 하며, 필드 타입에는 문자열, 정수나, 박스형이어야 합니다. 여러 필드를 기본키로 이용할 수 없으며, 문자열을 기본키로 사용하면 해당 필드는 인덱싱 됩니다.(@PrimaryKey 어노테이션은 암묵적으로 @Index 어노테이션을 설정합니다.)

### createOrUpdate()
객체를 생성하거나 수정할 수 있으며, 기본키가 없으면 새 객체를 생성하고, 이미 있다면 수정됩니다.

### Realm.createObject()
모든 필드가 기본 값으로 설정된 새로운 객체를 얻습니다. 이 경우 기본 키가 기본 값으로 설정이 되어 기존 객체와 충돌할 수 있습니다. 이 것을 막기 위해 비관리 객체를 만들어 값을 설정한 후 copyToRealm()를 통해 Realm으로 복사하는 것을 권장합니다.

```java
final MyObject obj = new MyObject();
obj.setId(42);
obj.setName("Fish");
realm.executeTransaction(new Realm.Transaction() {
    @Override
    public void execute(Realm realm) {
        // 이것은 Realm의 새 클래스를 만들거나 이미 객체가 있다면 예외를 던집니다.
        // (동일한 기본 키)
        realm.copyToRealm(obj);

        // 이는 같은 기본 키가 있다면 기존 객체를 갱신하거나
        // 기본 키 = 42인 객체가 없다면 새로운 객체를 만듭니다
        realm.copyToRealmOrUpdate(obj);
    }
});
```

## 관계

### Realm에서의 관계
모든 RealmObject는 같이 연결할 수 있습니다.

```java
public class Email extends RealmObject {
    private String address;
    private boolean active;
    // ... 세터와 게터들이 위치합니다
}

public class Contact extends RealmObject {
    private String name;
    private Email email; // 위에서 선언한 RealmObject
    // ... 세터와 게터들이 위치합니다
}
```

### 다 VS 다
RealmList<T> 필드를 이용해다대다 1:N, N:N 관게를 만들 수 있습니다.

```java
public class Contact extends RealmObject {
    public String name;
    public RealmList<Email> emails; // 여러개의 메일
}

public class Email extends RealmObject {
    public String address;
    public boolean active;
}
```

객체들을 만들고 Email 객체들을 Contact 객체에 추가하기 위해 RealmList.add()를 사용할 수 있습니다.

```java
realm.executeTransaction(new Realm.Transaction() {
    @Override
    public void execute(Realm realm) {
        Contact contact = realm.createObject(Contact.class);
        contact.name = "John Doe";

        Email email1 = realm.createObject(Email.class);
        email1.address = "john@example.com";
        email1.active = true;
        contact.emails.add(email1);

        Email email2 = realm.createObject(Email.class);
        email2.address = "jd@example.com";
        email2.active = false;
        contact.emails.add(email2);
    }
});
```




## 쓰기

### Commit

```java
Realm realm = Realm.getDefaultInstance();
realm.beginTransaction();

// 이 안에서 객체를 추가하거나 갱신합니다.
// ex) User user = realm.createObject(User.class);

realm.commitTransaction();
```

### Cancel

```java
realm.beginTransaction();

// 이 안에서 객체를 추가하거나 갱신합니다.
// ex) User user = realm.createObject(User.class);

realm.cancelTransaction();
```

### 객체 만들기 #1 (Realm을 통해 직접 인스턴스 생성 방법)

```java
realm.beginTransaction();
User user = realm.createObject(User.class); // 새 객체 만들기
user.setName("John");
user.setEmail("john@corporation.com");
realm.commitTransaction();
```

### 객체 만들기 #2 (인스턴스를 생성하고 나중에 추가하는 방법)

```java
User user = new User("John");
user.setEmail("john@corporation.com");

// 객체를 Realm으로 복사하기, 어떤 추가적인 변경도 realmUser에 있을 수 있습니다.
realm.beginTransaction();
User realmUser = realm.copytoRealm(user);
realm.commitTransaction();
```


### 트랜잭션 블록
beginTransaction(), commitTransaction(), cancelTransaction()을 관리하는대신에 자동으로 begin/commit 하는 방법

```java
realm.executeTransaction(new Realm.Transaction() {
	@Override
	public void execute(Realm realm) {
		User user = realm.createObject(User.class);
		user.setName("John");
		user.setEmail("john@corporation.com");
	}
});
```

### 비동기 트랜잭션
트랜잭션이 다른 트랜잭션들에 의해 블록되기 때문에, 백그라운드에서 모든 쓰기 작업을 하는 방법

```java
realm.executeTransactionAsync(new Realm.Transaction() {
            @Override
            public void execute(Realm bgRealm) {
                User user = bgRealm.createObject(User.class);
                user.setName("John");
                user.setEmail("john@corporation.com");
            }
        }, new Realm.Transaction.OnSuccess() {
            @Override
            public void onSuccess() {
                // Transaction was a success.
            }
        }, new Realm.Transaction.OnError() {
            @Override
            public void onError(Throwable error) {
                // Transaction failed and was automatically canceled.
            }
        });
```

비동기 트랜잭션인 RealmAsyncTask 객체는 트랜잭션이 끝나기 전에, 액티비티나, 프래그먼트를 끝내야 할 때 대기중인 트랜잭션을 취소하기 위해서도 사용

```java
public void onStop () {
    if (transaction != null && !transaction.isCancelled()) {
        transaction.cancel();
    }
}
```


## 질의

```
(질의와 속성 접근을 포함한) 모든 질의는 바로 처리되지 않습니다. 속성에 접근할 때에만 데이터를 읽습니다.
```


```java
public class User extends RealmObject {

    @PrimaryKey
    private String          name;
    private int             age;

    @Ignore
    private int             sessionId;

    // IDE에서 생성된 표준 게터와 세터들...
    public String getName() { return name; }
    public void   setName(String name) { this.name = name; }
    public int    getAge() { return age; }
    public void   setAge(int age) { this.age = age; }
    public int    getSessionId() { return sessionId; }
    public void   setSessionId(int sessionId) { this.sessionId = sessionId; }
}
```


```java
// Build the query looking at all users:
RealmQuery<User> query = realm.where(User.class);

// 질의 조건을 추가합니다
query.equalTo("name", "John");
query.or().equalTo("name", "Peter");

// 질의를 수행합니다
RealmResults<User> result1 = query.findAll();

// 같은 일들을 한번에 합니다 ("Fluent interface"):
RealmResults<User> result2 = realm.where(User.class)
                                  .equalTo("name", "John")
                                  .or()
                                  .equalTo("name", "Peter")
                                  .findAll();
```


### 조건
모든 조건이 모든 데이터 타입을 지원하지는 않습니다.

- between()
- greaterThan()
- lessThan()
- greaterThanOrEqualTo()
- lessThanOrEqualTo()
- equalTo()
- notEqualTo()
- contains()
- beginsWith()
- endsWith()
- isNull
- isNotNull()
- isEmpty()
- isNotEmpty()


### 논리 연산자
각 조건은 암묵적으로 AND 연산이 지원됩니다. or 연산을 위해서는 명시적으로 or()를 사용해야합니다.

```java
RealmResults<User> r = realm.where(User.class)
                            .greaterThan("age", 10)  // 암묵적인 AND
                            .beginGroup()
                                .equalTo("name", "Peter")
                                .or() // or() 사용
                                .contains("name", "Jo")
                            .endGroup()
                            .findAll();
```

게다가, not()으로 부정 조건을 만들 수 있습니다. not() 연산자는 하위 조건을 부정하기 위해서는 오직 beginGroup()/endGroup()과 함께 사용할 수 있습니다.

### 정렬

```java
RealmResults<User> result = realm.where(User.class).findAll();
result = result.sort("age"); // 오름차순으로 정렬
result = result.sort("age", Sort.DESCENDING);

```


### 자동 갱신 결과
RealmResults는 기반 데이터에 의해 라이브로 자동 갱신되는 뷰입니다. 데이터를 수정하면 질의는 다음 루프 이벤트인 RealmResults에 반영됩니다.

```java
final RealmResults<Dog> puppies = realm.where(Dog.class).lessThan("age", 2).findAll();
puppies.size(); // => 0

realm.executeTransaction(new Realm.Transaction() {
    @Override
    void public execute(Realm realm) {
        Dog dog = realm.createObject(Dog.class);
        dog.setName("Fido");
        dog.setAge(1);
    }
});

puppies.addChangeListener(new RealmChangeListener() {
    @Override
    public void onChange(RealmResults<Dog> results) {
      // results와 puppies는 같은 시간에 값이 올라갑니다
      results.size(); // => 1
      puppies.size(); // => 1
    }
});
```

### 집합

```java
RealmResults<User> results = realm.where(User.class).findAll();
long   sum     = results.sum("age").longValue();
long   min     = results.min("age").longValue();
long   max     = results.max("age").longValue();
double average = results.average("age");

long   matches = results.size();
```


### 반복자

```java
RealmResults<User> results = realm.where(User.class).findAll();
for (User u : results) {
    // ... 객체에 대해 뭔가 합시다 ...
}
```

```java
RealmResults<User> results = realm.where(User.class).findAll();
for (int i = 0; i < results.size(); i++) {
    User u = results.get(i);
    // ... 객체에 대해 뭔가 합시다 ...
}
```


### 삭제

```java
// 질의의 결과를 얻습니다
final RealmResults<Dog> results = realm.where(Dog.class).findAll();

// 데이터에 대한 모든 변경은 트랜잭션에서 이루어져야 합니다
realm.executeTransaction(new Realm.Transaction() {
    @Override
    public void execute(Realm realm) {
        // 하나 맞는 데이터를 삭제합니다
        results.deleteFirstFromRealm();
        results.deleteLastFromRealm();

        // 하나의 객체를 삭제합니다
        Dog dog = results.get(5);
        dog.deleteFromRealm();

        // 전체 맞는 데이터를 삭제합니다
        results.deleteAllFromRealm();
    }
});
```

### 비동기 질의

```java
RealmResults<User> result = realm.where(User.class)
                              .equalTo("name", "John")
                              .or()
                              .equalTo("name", "Peter")
                              .findAllAsync(); // 비동기
```

질의가 즉시 RealmResults<User>를 반환하는 부분은 일종의 규칙입니다. 만약 REalmResults가 갱신이 완료되었을때 노티를 받기를 원한다면 RealmChangeListener를 등록합니다. 이 리스너는 Realm의 마지막 값이 바뀔 때마다 RealmResults를 갱신합니다.

#### 콜백 등록하기

```java
private RealmChangeListener callback = new RealmChangeListener() {
    @Override
    public void onChange() { // 질의과 완료될 때 한번 호출되고 매 업데이트마다 호출됩니다
    // use the result
    }
};

public void onStart() {
    RealmResults<User> result = realm.where(User.class).findAllAsync();
    result.addChangeListener(callback);
}
```

메모리릭을 막기 위해 리스너들을 해제하는걸 잊지말아야 합니다.

```java
public void onStop () {
    result.removeChangeListener(callback); // 특정 리스너를 제거합니다
    // or
    result.removeChangeListeners(); // 전체 등록된 리스너들을 제거한다
}
```


#### 질의가 끝났는지 확인
동기적으로 획득한 RealmResults의 isLoaded()를 호출하면 항상 true를 반환

```java
if (result.isLoaded()) {
  // 결과는 지금 사용할 수 있습니다
}
```


#### 강제로 질의를 동기적으로 로딩
현재 스레드를 블록하고 질의를 다시 동기적으로 합니다.

```java
result.load() // 주의하세요. 이 코드는 현재 스레드를 반환될때까지 멈추게 합니다
```


## JSON
문자열, JSONObject InputStream으로 표현된 JSON을 Realm에 직접 RealmObject로 추가하는 것이 가능합니다. Realm은 RealmObject에 정의되지 않는 JSON 프로퍼티들은 무시합니다. Realm.createObjectFromJson()을 이용해서 단일 객체를 추가할 수 있고 Realm.createAllFromJson()를 이용하여 객체들의 리스트를 추가할 수 있습니다.

```java
// A RealmObject that represents a city
public class City extends RealmObject {
    private String city;
    private int id;
    // 게터와 세터들이 있습니다 ...
}

// 문자열을 삽입합니다
realm.executeTransaction(new Realm.Transaction() {
    @Override
    public void execute(Realm realm) {
        realm.createObjectFromJson(City.class, "{ city: \"Copenhagen\", id: 1 }");
    }
});

// InputStream을 이용하여 여러 아이템을 삽입합니다
realm.executeTransaction(new Realm.Transaction() {
    @Override
    public void execute(Realm realm) {
        try {
            InputStream is = new FileInputStream(new File("path_to_file"));
            realm.createAllFromJson(City.class, is);
        } catch (IOException e) {
            throw new RuntimeException(e);
        }
    }
});
```


## 안드로이드 프레임워크 스레드를 다루는 전략

### AsyncTask

```java
private class DownloadOrders extends AsyncTask<Void, Void, Long> {
    protected Long doInBackground(Void... voids) {
        // 이제 백그라운드 스레드입니다.

        // Realm을 엽니다.
        Realm realm = Realm.getDefaultInstance();
        try {
            // Realm을 사용합니다.
            realm.createAllFromJson(Order.class, api.getNewOrders());
            Order firstOrder = realm.where(Order.class).findFirst();
            long orderId = firstOrder.getId(); // Id of order
            return orderId;
        } finally {
            realm.close();
        }
    }

    protected void onPostExecute(Long orderId) {
        // 안드로이드 메인스레드로 돌아왔습니다.
        // orderId를 가지고 order에 대해 Realm에 질의하거나
        // 어떤 연산을 수행합시다.
    }
}
```

### IntentService

```java
public class OrdersIntentService extends IntentService {
    public OrdersIntentService(String name) {
        super("OrdersIntentService");
    }

    @Override
    protected void onHandleIntent(Intent intent) {
        // 이제 백그라운드 스레드입니다.

        // Realm을 엽시다.
        Realm realm = Realm.getDefaultInstance();
        // Realm을 사용합니다.
        realm.createAllFromJson(Order.class, api.getNewOrders());
        Order firstOrder = realm.where(Order.class).findFirst();
        long orderId = firstOrder.getId(); // order의 아이디
        realm.close();
    }
}
```
