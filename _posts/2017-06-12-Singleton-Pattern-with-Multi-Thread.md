---
layout: post
title: "멀티스레드 환경에서의 싱글톤 패턴"
image: '/assets/img/'
description: '멀티스레드 환경에서의 싱글톤 패턴'
tags:
- Design Pattern
- JAVA
categories:
- JAVA
---

## 자바 멀티스레드 환경에서 싱글톤 패턴

### 싱글톤 패턴
GoF(Gang of Four) 디자인 패턴 중 하나로 생성자가 여러 차례 호출되더라도 실제로 생성되는 인스턴스는 하나이고, 최초 생성 이후에 호출된 생성자는 최초에 생성한 객체를 리턴하는 형태

### 일반적인 형태

```java
class Singleton {
    private static Singleton ourInstance = null;

    public static Singleton getInstance() {

        if (ourInstance == null) {
            ourInstance = new Singleton();
        }

        return ourInstance;
    }

    private Singleton() {}
}
```

### 호출 방법
```java
// 보통 인스턴스 호출 방법(새로운 인스턴스가 생성)
Singleton singletonInstance = new Singleton();

// 싱글톤 호출 방법(한번 생성한 인스턴스가 지속적으로 사용)
Singleton singletonInstance = Singleton.getInstance();

```


### 문제점

보통의 경우에는 해당 코드로도 전혀 문제가 되지 않지만, 멀티 스레드 환경에서는 잠재적 문제를 가지고 있다.

두 개 이상의 스레드가 인스턴스를 획득하기 위해 getInstance9) 메서드에 진입하여 경합을 벌이는 과정에서 서로 다른 두 개의 인스턴스가 만들어지는 형태가 발생할 여지가 있다.

### 해결방법 1)
인스턴스를 필요할 때 생성하지 않고, 처음부터 인스턴스를 만들어 버린다, 단 인스턴스를 미리 만들어 버리게 되면, 불필요한 시스템 리소스를 낭비할 가능성이 있다.

```java
public class Singleton {
	private static Singleton ourInstance = new Singleton();

	public static Singleton getInstance() {
		return ourInstance;
	}

	private Singleton() {}

```

### 해결방법 2)
getInstance() 메서드를 동기화시킨다. 대신 메서드를 동기화 시키면 일반적으로 성능이 100배 정도는 저하된다.

```java

class Singleton {
    private static Singleton ourInstance = null;

    public synchronized static Singleton getInstance() {

        if (ourInstance == null) {
            ourInstance = new Singleton();
        }

        return ourInstance;
    }

    private Singleton() {}
}

```


### 해결방법 3)
DCL 기법을 사용한다. 현재는 권고하지 않는 방법이다.

```java
class Singleton {
    private static Singleton ourInstance = null;

    public static Singleton getInstance() {

        if (ourInstance == null) {
            synchronized (Singleton.class) {
                if (ourInstance == null) {
                    ourInstance = new Singleton();
                }
            }
        }

        return ourInstance;
    }

    private Singleton() {}
}
```

### 해결방법 4)
LazyHolder 기법으로 synchronized도 필요 없고, 자바 버전도 상관없는 방법으로, Singleton 클래스의 getInstance() 메서드에서 LazyHolder.INSTANCE를 참조하는 순간 Class가 로딩되며 초기화가 진행된다. Class를 로딩하고 초기화하는 시점은 thread-safe를 보장하기 때문에 volatile이나 synchronized 같은 키워드가 없어도 된다.

```java
class Singleton {
    private Singleton() {}

    public static Singleton getInstance() {
        return LazyHolder.INSTANCE;
    }

    private static class LazyHolder {
        private static final Singleton INSTANCE = new Singleton();
    }
}
```
