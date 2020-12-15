---
layout: post
title: "객체지향 설계 5대 원칙 - SOLID"
image: '/assets/img/'
description: 'SOLID에 대한 설명 및 고찰'
tags:
- OOP
- SOLID
- Clean Code
- Refactoring
categories:
- SOLID
---

객체지향 설계 5대 원칙인 SOLID에 대해서 알아보도록 하겠습니다.

## 객체지향 설계 5대 원칙 - SOLID

- `S`RP (Single responsibility principle) - 단일 책임 원칙
- `O`CP (Open-closed principle) - 개방 폐쇄 원칙
- `L`SP (Liskov substitution principle) - 리스코브 치환 원칙
- `I`SP (Interface segregation principle) - 인터페이스 분리 원칙
- `D`IP (Dependency inversion principle) - 의존 역전 원칙


---

### 1. SRP (Single responsibility principle) - 단일 책임 원칙

> 단일 클래스는 단 한개의 책임을 가져야 한다 (=클래스를 변경하는 이유는 단 한개여야 한다)

Before)

```java
/*
 * 현재는 UserSettingService라는 클래스의 두개의 책임이 있다
 * 1. 변경
 * 2. 접근 권한에 대한 부분
 */
 
public class UserSettingService {

  public void changeEmail(User user) {
    if(checkAccess(user)) {
       // Grant option to change
    }
  }
  
  public boolean checkAccess(User user) {
    // Verify if the user is valid.
  }
  
}
```

After)

```java
/*
 * 현재는 UserSettingService라는 클래스의 두개의 책임을 둘로 나눔
 * 1. 변경 (UserSettingService.class)
 * 2. 접근 권한에 대한 부분 (SecurityService)
 */

public class UserSettingService {

  public void changeEmail(User user) {
    if(SecurityService.checkAccess(user)) {
       // Grant option to change
    }
  }
  
}

public class SecurityService {

  public static boolean checkAccess(User user) {
    // Check the access.
  }
  
}

```

---


### 2. OCP (Open-closed principle) - 개방 폐쇄 원칙

> 기능을 변경하거나 확장할 수 있으면서 그 기능을 사용하는 코드는 수정하지 않는다

#### OCP 원칙이 깨질 때의 주요 현상

(1) 다운 캐스팅을 한다

Example)

```java
public void drawCharacter(Character character) {
    if (character instanceof Missile) { // 타입 확인 (주요 현상 #1)
        Missile missile = (Missile) character; // 다운 캐스팅 (주요 현상 #2)
        missile.drawSpecific(); // 미사일일 경우는 drawSpecific() 호출
    }
    else {
        character.draw(); // 미사일 외의 경우는 draw() 호출
    }
} 
```

(2) 비슷한 if - else 블록이 존재한다

```java
public class Enemy extends Character {
    private int pathPattern;
    
    public Enemy(int pathPattern) {
        this.pathPattern = pathPattern;
    }
    
    public void draw() {
        // 반복적인 if - else 블록 발생 (주요 현상)
        if (pathPattern == 1) {
            x += 5;
            y += 5;
        }
        else if (pathPattern == 2) {
            x += 10;
            y += 10;
        }
        else if (pathPattern == 3) {
            x += 15;
            y += 15;
        }
        else {
            x += 20;
            y += 20;
        }
    }
}
```

---

Before)

```java

public class Rectangle {
    public double Width { ... }
    public double Height { ... }
}

public class AreaCalculator {
    public double Area(object[] shapes) {
        double area = 0;
        
        foreach (shape instanceof shapes) {
            if (shape instanceof Rectangle) {
                Rectangle rectangle = (Rectangle) shape;
                area += rectangle.Width * rectangle.Height;
            }
            else {
                Circle circle = (Circle)shape;
                area += circle.Radius * circle.Radius * Math.PI;
            }
        }
    
        return area;
    }
}


```

After)

```java

public interface class Shape {
    double Area();
}


public class Rectangle implements Shape {
    public double Width { ... }
    public double Height { ... }
    
    @Override
    public double Area() {
        return Width*Height;
    }
}

public class Circle implements Shape {
    public double Radius { ... }
    
    @Override
    public double Area() {
        return Radius * Radius * Math.PI;
    }
}


public double Area(Shape[] shapes) {
    double area = 0;
    foreach (var shape in shapes) {
        area += shape.Area();
    }

    return area;
}


```


---


### 3. LSP (Liskov substitution principle) - 리스코브 치환 원칙

> 상위 타입의 객체를 하위 타입의 객체로 치환해도 상위 타입을 사용하는 프로그램은 정상적으로 동작해야 한다


#### LSP 원칙이깨질 때의 주요 현상

(1) 타입을 확인하는 기능(java의 instanceof 연산자)를 사용한다

```java

public class Coupon {
    public int calcuateDiscountAmount(Item item) {
        if (item instanceof SpecialItem) // LSP 위반
            return 0;
        
        return item.getPrice() * discountRate;
    }
}
 
``` 

---

Before)

```java

public class Coupon {
    public int calcuateDiscountAmount(Item item) {
        if (item instanceof SpecialItem) // LSP 위반
            return 0;
        
        return item.getPrice() * discountRate;
    }
}

```

After)

```java

public class Item {
    public boolean isDiscountAvailable() {
        return true;
    }
}

public class SpecialItem extends Item {
    // 하위 타입에서 알맞게 오버라이딩
    @Override
    public boolean isDiscountAvailable() {
        return false;
    }
}

public class Coupon {
    public int calcuateDiscountAmount(Item item) {
        if (!item.isDiscountAvailable()) // instanceof 연산자 제거
            return 0;
        
        return item.getPrice() * discountRate;
    }
}

```

---


### 4. ISP (Interface segregation principle) - 인터페이스 분리 원칙

> 인터페이스는 그 인터페이스를 사용하는 클라이언트를 기준으로 분리해야 한다

Before)

```java

// 하나의 인터페이스를 모든 클라이언트가 구현하고 있음

public interface ArticleService {
    void list();
    void write();
    void delete();
}

public class UiList implements ArticleService {
    @Override
    public void list() {}
    
    @Override
    public void write() {}
    
    @Override
    public void delete() {}
}

public class UiWist implements ArticleService {
    @Override
    public void list() {}
    
    @Override
    public void write() {}
    
    @Override
    public void delete() {}
    
}

public class UiDist implements ArticleService {
    @Override
    public void list() {}
    
    @Override
    public void write() {}
    
    @Override
    public void delete() {}
    
}

```

After)

```java

// 각각의 클라이언트별로 Interface를 구분

public interface ArticleListService {
    void list();
}

public interface ArticleWriteService {
    void Write();
}

public interface ArticleDeleteService {
    void Delete();
}

public class UiList implements ArticleListService {
    @Override
    public void list() {}
}

public class UiWist implements ArticleWriteService {
    @Override
    public void write() {}
}

public class UiDist implements ArticleDeleteService {
    @Override
    public void delete() {}
}

```

---


### 5. DIP (Dependency inversion principle) - 의존 역전 원칙

> 고수준 모듈은 저수준 모듈의 구현에 의존해서는 안 된다 저수준 모듈이 고수준 모듈에서 정의한 추상 타입에 의존해야 한다


- 고수준 모듈 (어떤 의미있는 단일 기능을 제공하는 모듈)
    - 바이트 데이터를 읽어와
    - 암호화 하고
    - 결과 바이트 데이터를 쓴다

- 저수준 모듈 (고수준 모듈의 기능을 구현하기 위해 필요한 개별 기능 = 좀 더 작은 모듈)
    - 파일에서 바이트 데이터를 읽어온다
    - AES 알고리즘으로 암호화 한다
    - 파일에 바이트 데이터를 쓴다
    
    
> Key Point) 중간에 Interface와 같은 추상화를 통해서 고수준 모듈과 저수준 모듈이 모두 추상 타입에 의존하게 만든다.

Before)

```java
// "Low level Module" Mechanism equivilant
public class Logger {
    public void logInformation(String logInfo) {
        System.out.println(logInfo);
    }
}

// "High level module" Policy equivalent.
public class Foo {
    // direct dependency of a low level module.
    private Logger logger = new Logger();

    public void doStuff() {
        logger.logInformation("Something important.");
    }
}
```

After)

```java
public interface ILogger {
    void logInformation(String logInfo);
}

public class Logger implements ILogger {
    @Override
    public void logInformation(string logInfo) {
        System.out.println(logInfo);
    }
}

public class Foo {
    private ILogger logger;
    
    public void setLoggerImpl(ILogger loggerImpl) {
        this.logger = loggerImpl;
    }

    public void doStuff() {
        logger.logInformation("Something important.");
    }
}

// Usage
Foo foo = new Foo();
ILogger logger = new Logger();
foo.setLoggerImpl(logger);
foo.doStuff();

```

