---
layout: post
title: 싱글톤 패턴이란?
tags: [CS, 디자인 패턴]
color: rgb(51,123,225) 
author: aerimforest
excerpt_separator: <!--more-->
---

## 싱글톤 패턴
싱글톤 패턴(Singleton Pattern)이란, 하나의 클래스에 오직 하나의 인스턴스만 가지는 패턴을 말한다.  

하나의 인스턴스를 만들어 놓고 해당 인스턴스를 다른 모듈들이 공유하며 사용하기 때문에 인스턴스를 생성할 때 드는 비용이 줄어든다는 장점이 있다. 보통 <u>데이터베이스</u>를 연결할 때 많이 사용된다.   

<!--more-->

<br><br>

## 싱글톤 패턴의 구현
싱글톤 패턴을 구현하는 방법은 크게 두 가지가 있다.  

#### 1.&nbsp;Eager initialization  

```java
// Eager initialization  

public class Singleton {
   // SingleObject 객체 생성
   // 객체가 오직 하나임을 보장하기 위해 정적(static) 필드 사용
   private static final Singleton INSTANCE = new Singleton();

   // 외부에서 생성자를 통해 객체를 생성할 수 없도록 생성자의 접근 범위를 private으로 제한
   private Singleton() {}

   // 클래스를 인스턴스화하지 않고 호출할 수 있도록 static 선언
   public static Singleton getInstance() {
      return INSTANCE;
   }
}
```
이른 초기화 방식에서 싱글톤 인스턴스는 싱글톤 변수가 처음 사용될 때가 아니라, <u>초기화될 때 생성</u>된다.
<br><br>
이 방식은 클래스가 로드될때 싱글톤 객체가 생성되므로 `thread-safe` 하다는 장점이 있지만, 현재 프로그램 실행에는 사용되지 않기 때문에 시스템 자원을 불필요하게 소모할 수 있다는 단점이 있다.
<br><br>
또한, 싱글톤 인스턴스가 계산 비용이 많이 들거나 리소스를 많이 사용하는 경우 시스템 성능이 저하될 수 있다.
<br><br>
위의 예제 코드에서 볼 수 있듯이, 객체가 오직 하나임을 보장하기 위해 `정적(static)` 필드를 사용하는데 이렇게 하면 모든 객체가 공유하는 필드를 만들 수 있으며, 한 번만 생성되고 별도의 메모리 공간에 저장할 수 있다.  
<br><br>
#### 2.&nbsp;Lazy initialization  

```java
// Lazy initialization  

public final class Singleton {

    private static Singleton INSTANCE = null;

    private Singleton() {}

    public static Singleton getInstance() {
        if (INSTANCE == null) {
            INSTANCE = new Singleton();
        }
        return INSTANCE;
    }
} 
```
지연 초기화 방식에서는 <u>static getInstance() 메소드가 처음 호출될 때</u> 싱글톤 인스턴스가 생성된다.  

따라서 싱글톤 인스턴스는 꼭 필요한 경우에만 시스템 리소스를 소비하게 된다.  

하지만 지연 초기화를 사용할 경우 thread-safe 하지 않다. 

멀티 스레드 프로그램에서 싱글톤 인스턴스는 싱글톤 클래스를 동시에 사용하여 여러 번 생성될 수 있기에 하나만 생성되어야 하는 인스턴스가 두 개 이상 생성될 수 있기 때문이다.

또한, 싱글톤 객체를 만드는 데 비용이 많이 드는 경우 사용 가능한 시스템 리소스가 많이 소모될 수 있다. 따라서 `동기화`를 사용하여 한 번에 하나의 스레드만 getInstance()를 실행할 수 있도록 해야 한다.  
<br>

```java
// 동기화 기반 Lazy initialization  

public class Singleton {
    private static Singleton INSTANCE;
 
    private Singleton() {}
 
    // 한 번에 하나의 스레드만 getInstance()를 실행할 수 있도록
    public static synchronized Singleton getInstance() {
        if (INSTANCE == null)
            INSTANCE = new Singleton();
        return INSTANCE;
    }
}
```

위의 코드는 getInstance 메소드에 `동기화`를 적용하여 thread-safe 해졌다.   
하지만 싱글톤 객체를 만드는 동안 매번 동기화를 사용하는 것은 비용이 많이 들고, 프로그램의 성능을 저하시킬 수 있다는 문제가 있다.   
<br><br>
이때 `Double Checking Locking`을 사용하면 getInstance()에서 동기화되는 영역을 줄여 성능 저하를 막을 수 있다.  

`Double Checking Locking`은 lock을 획득하기 전에 lock 기준을 먼저 체크함으로써 lock을 획득하는 오버헤드를 줄일 수 있는 `디자인 패턴`이다.  

아래 예시에서 볼 수 있듯이, 먼저 객체를 만들어야 하는 지를 체크하고, 객체를 만들어야 할 때만 lock을 얻도록 하면 동기화의 성능 문제를 개선할 수 있다.

```java
// 동기화 & DCL 기반 Lazy initialization  

public class Singleton {
    // 변수를 캐시 메모리가 아닌 메인 메모리에서 읽어오도록 volatile 선언
    // 모든 스레드가 싱글톤 인스턴스의 업데이트된 보기를 갖도록 
    private static volatile Singleton INSTANCE  = null;
 
    private Singleton() {}
 
    public static Singleton getInstance() {
        if (INSTANCE == null) {
            // for thread safe
            synchronized (Singleton.class) {
                // check again as multiple threads
                // can reach above step
                if (INSTANCE == null) {
                    INSTANCE = new Singleton();
                }
            }
        }
        return INSTANCE;
    }
}
```


<br><br>

## 싱글톤 패턴의 장점
1. 메모리 낭비 방지  
하나의 인스턴스를 만들어 놓고 해당 인스턴스를 다른 모듈들이 공유하며 사용하기 때문에 메모리 낭비를 방지할 수 있다.
<br><br>
2. 데이터 공유  
전역으로 사용되는 인스턴스이기 때문에 다른 클래스의 인스턴스들이 접근하여 사용할 수 있다. 데이터베이스를 연결할 때 주로 싱글톤 패턴을 사용하는 이유이기도 하다.<br><br>
하지만 여러 클래스의 인스턴스에서 싱글톤 인스턴스의 데이터에 동시에 접근하게 되면 동시성 문제가 발생할 수 있기에 주의해야 한다.

<br><br>

## 싱글톤 패턴의 단점
1. 높은 의존성  
싱글톤 패턴은 사용하기 쉽고 실용적이지만 모듈 간의 결합을 강하게 만들 수 있다는 단점이 있다. 의존 관계상 클라이언트가 구체 클래스에 의존하게 되므로 SOLID 원칙 중 `DIP`를 위반하게 되고, OCP 원칙 또한 위반할 수 있다.<br><br>이때 `의존성 주입`을 통해 모듈 간의 결합을 좀 더 느슨하게 만들 수 있다. 자세한 내용은 아래에서 알아보자.  
<br>  
1. 테스트의 어려움  
싱글톤 패턴은 `TDD(Test Driven Development)`에서 문제가 된다. TDD에서는 독립적인 단위 테스트가 이루어져야 하는데, 싱글톤 패턴을 사용할 경우 미리 생성된 하나의 인스턴스를 기반으로 구현하기 때문에 테스트가 서로 종속되어있다. 따라서 단위 테스트도 어려울뿐더러 테스트의 순서도 자유롭지 않다.   

<br><br>

## 의존성 주입
의존성 주입(Dependency Ingection)은 말 그대로 외부에서 새로운 의존성을 생성하여 내부에 주입하는 것이다.  

```kotlin
class Car {

    private val engine = Engine()

    fun start() {
        engine.start()
    }
}

fun main(args: Array) {
    val car = Car()
    car.start()
}
```

이 예제에서 Car 클래스 내에서 Engine 클래스를 참조하고 있기 때문에 Car와 Engine은 밀접하게 연결되어 있다. 따라서 Engine 클래스에 수정사항이 발생했을 때 Car 클래스도 영향을 받게 된다.  
<br><br>
여기에 의존성을 주입하면 어떻게 될까?  
 
`안드로이드`에서 의존성 주입을 실행하는 방법은 크게 두 가지가 있다. 
 
1️⃣ 생성자 주입: 클래스의 종속 항목을 생성자에 전달  

```kotlin
class Car(private val engine: Engine) {
    fun start() {
        engine.start()
    }
}

fun main(args: Array) {
    val engine = Engine()
    val car = Car(engine)
    car.start()
}
```  
<br>
2️⃣ 필드 주입(or Setter 주입): 필드 삽입을 사용하면 종속 항목은 클래스가 생성된 후 인스턴스화  

```kotlin
class Car {
    lateinit var engine: Engine

    fun start() {
        engine.start()
    }
}

fun main(args: Array) {
    val car = Car()
    car.engine = Engine()
    car.start()
}
```
위의 예제는 main(외부)에서 Engine 객체를 생성하고, 이를 사용하여 Car 객체의 engine 필드에 setter를 적용하는 코드이다.  
<br><br>
의존성 주입을 하기 전의 Car와 Engine 클래스의 관계가 다음과 같았다면,
<img width="400" alt="" src="https://user-images.githubusercontent.com/52696359/205508067-206fef72-262d-48a5-934b-259959504d24.png"><br><br><br>
의존성 주입 후 두 클래스의 관계는 다음과 같이 변하게 된다.
<img width="400" alt="" src="https://user-images.githubusercontent.com/52696359/205508160-02577db6-e0ca-404a-b3f5-a1d860526ad9.png"><br><br>  

따라서 의존성 주입의 장점을 정리해보면 다음과 같다.

```java
1. 코드 재사용 가능
2. 리팩터링 편의성
3. 테스트 편의성
```

<br><br>

## References
- [tutorialspoint/design_pattern/singleton_pattern](https://www.tutorialspoint.com/design_pattern/singleton_pattern.htm)
- [developer.android.com/dependency-injection](https://developer.android.com/training/dependency-injection)
- [betterprogramming/what-is-a-singleton](https://betterprogramming.pub/what-is-a-singleton-2dc38ca08e92)
- [geeksforgeeks/singleton-design-pattern](https://www.geeksforgeeks.org/singleton-design-pattern/)