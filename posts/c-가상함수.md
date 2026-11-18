# [C++] 가상함수

> 디벨로그에서 쓴 글 · 2026-11-17
>
> https://develog.develrocket.com/@muchan/c-가상함수

[https://modoocode.com/210](https://modoocode.com/210)

[https://modoocode.com/211](https://modoocode.com/211)

위 사이트에서 공부한 내용 정리입니다

---

## is-a 관계

상속은 is-a 관계이다

Manager라는 클래슫가 Employee를 상속받으면 Manager is a Employee이고,

<img src="https://data.develog.develrocket.com/upload/develog/muchan/1794915452194-d4m9c3/image.png" alt="" width="219" height="239">

그림은 이렇게 표현된다고 한다.

파생 클래스에서 기반 클래스로 화살표가 향하게 된다

## has-a 관계

모든 상속 관계를 is-a로 표현할 수 있는건 아니고 has-a 관계로도 표현할 수 있다

만약 자동차가 엔진과 브레이크를 가진다하면 자동차 has a 엔진, 브레이크가 되는 것이다

## 업캐스팅

### 코드

```
#include <iostream>
#include <string>

class Base {
  std::string s;

 public:
  Base() : s("기반") { std::cout << "기반 클래스" << std::endl; }

  void what() { std::cout << s << std::endl; }
};
class Derived : public Base {
  std::string s;

 public:
  Derived() : s("파생"), Base() { std::cout << "파생 클래스" << std::endl; }

  void what() { std::cout << s << std::endl; }
};
int main() {
  Base p;
  Derived c;

  std::cout << "=== 포인터 버전 ===" << std::endl;
  Base* p_c = &c;
  p_c->what();

  return 0;
}
```

### 실행 결과

```
기반 클래스
기반 클래스
파생 클래스
=== 포인터 버전 ===
기반
```

p_c는 Base 객체이므로 what()을 호출하면 Base의 what이 호출되는 것이다

<img src="https://data.develog.develrocket.com/upload/develog/muchan/1794915452200-6gvpyb/image.png" alt="" width="408" height="310">

사진과 같은 구조이다

## 다운 캐스팅

### 코드

```
#include <iostream>
#include <string>

class Base {
  std::string s;

 public:
  Base() : s("기반") { std::cout << "기반 클래스" << std::endl; }

  void what() { std::cout << s << std::endl; }
};
class Derived : public Base {
  std::string s;

 public:
  Derived() : s("파생"), Base() { std::cout << "파생 클래스" << std::endl; }

  void what() { std::cout << s << std::endl; }
};
int main() {
  Base p;
  Derived c;

  std::cout << "=== 포인터 버전 ===" << std::endl;
  Derived* p_p = &p;
  p_p->what();

  return 0;
}
```

위의 코드는 다운 캐스팅을 구현한 코드이다. 하지만 실행해보면 컴파일 에러가 발생한다.

<img src="https://data.develog.develrocket.com/upload/develog/muchan/1794915452202-dlv05i/image.png" alt="" width="491" height="304">

사진과 같은 구조로 Derived의 what함수가 Base객체인 p에 정의되어 있지 않기 때문이다

다운 캐스팅은 static_cast로 형 변환 시 컴파일 에러를 피할 수 있지만 런타임 에러가 발생한다

그래서 dynamic_cast로 컴파일 타임에 오류를 발견할 수 있지만, 다운 캐스팅은 확실하지 않으면 사용하지 않는게 좋다

## virtual 키워드

### 코드

```
#include <iostream>

class Base {

 public:
  Base() { std::cout << "기반 클래스" << std::endl; }

  virtual void what() { std::cout << "기반 클래스의 what()" << std::endl; }
};
class Derived : public Base {

 public:
  Derived() : Base() { std::cout << "파생 클래스" << std::endl; }

  void what() { std::cout << "파생 클래스의 what()" << std::endl; }
};
int main() {
  Base p;
  Derived c;

  Base* p_c = &c;
  Base* p_p = &p;

  std::cout << " == 실제 객체는 Base == " << std::endl;
  p_p->what();

  std::cout << " == 실제 객체는 Derived == " << std::endl;
  p_c->what();

  return 0;
}
```

### 실행 결과

```
기반 클래스
기반 클래스
파생 클래스
 == 실제 객체는 Base == 
기반 클래스의 what()
 == 실제 객체는 Derived == 
파생 클래스의 what()
```

위의 코드에서 Base의 what을 호출했지만 what을 virtual 키워드로 선언했기 때문에

Derived의 what이 호출된 것을 알 수 있다

### 동적 바인딩

위의 코드에서 p_c->what()은 아래와 같고

```
p_c->what();

"흠, p_c 는 Base 포인터니까 Base 의 what() 을 실행해야지"
"어 근데 what 이 virtual 이네?"

"잠깐. 이거 실제 Base 객체 맞어? 아니네 Derived 객체네"
"그럼 Derived 의 what 을 실행해야지"
```

p_p->what()은 아래와 같은 흐름으로 실행된다고 한다

```
p_p->what();

"흠, p_c 는 Base 포인터니까 Base 의 what() 을 실행해야지"
"어 근데 what 이 virtual 이네?"

"잠깐. 이거 실제 Base 객체 맞어? 어 맞네."
"Base 의 what 을 실행하자"
```

이렇게 어떤 함수가 실행되는지 런타임에 결정되는 것을 동적 바인딩이라고 한다

## override 키워드

C++ 11 에서는 파생 클래스에서 기반 클래스의 가상 함수를 오버라이드 하는 경우

override 키워드를 통해서 명시적으로 나타낼 수 있다

override를 사용하면 실수로 오버라이드를 하지 않는 경우를 막을 수 있다고 한다.

### 다형성

하나의 메서드를 호출했음에도 여러가지 다른 작업들을 하는 것

## virtual 소멸자

### 코드

```
#include <iostream>

class Parent {
 public:
  Parent() { std::cout << "Parent 생성자 호출" << std::endl; }
  ~Parent() { std::cout << "Parent 소멸자 호출" << std::endl; }
};
class Child : public Parent {
 public:
  Child() : Parent() { std::cout << "Child 생성자 호출" << std::endl; }
  ~Child() { std::cout << "Child 소멸자 호출" << std::endl; }
};
int main() {
  std::cout << "--- 평범한 Child 만들었을 때 ---" << std::endl;
  { Child c; }
  std::cout << "--- Parent 포인터로 Child 가리켰을 때 ---" << std::endl;
  {
    Parent *p = new Child();
    delete p;
  }
}
```

### 실행 결과

```
--- 평범한 Child 만들었을 때 ---
Parent 생성자 호출
Child 생성자 호출
Child 소멸자 호출
Parent 소멸자 호출
--- Parent 포인터로 Child 가리켰을 때 ---
Parent 생성자 호출
Child 생성자 호출
Parent 소멸자 호출
```

여기서 마지막에 Child형을 할당 받은 Parent객체의 소멸자는 비정상적인 것을 알 수 있다

이걸 처리를 하지 않으면 메모리 누수가 날 것이다

이걸 해결해주는게 virtual 소멸자이다

```
virtual ~Parent() { std::cout << "Parent 소멸자 호출" << std::endl; }
```

그냥 단순하게 소멸자에 virtual을 붙이면 된다

이후 실행하면 아래와 같은 실행 결과가 나온다고 한다

```
--- 평범한 Child 만들었을 때 ---
Parent 생성자 호출
Child 생성자 호출
Child 소멸자 호출
Parent 소멸자 호출
--- Parent 포인터로 Child 가리켰을 때 ---
Parent 생성자 호출
Child 생성자 호출
Child 소멸자 호출
Parent 소멸자 호출
```

## 가상함수 구현 원리

이렇게보면 모든 함수를 virtual로 선언하면 되는거 아니야? 라는 의문이 생긴다

실제로 자바는 모든 함수들이 디폴트로 virtual로 선언된다고 한다

C++에서는 virtual로 선언해야하는 이유는 약간의 오버헤드가 발생하기 때문이다

### 코드

```
class Parent {
 public:
  virtual void func1();
  virtual void func2();
};
class Child : public Parent {
 public:
  virtual void func1();
  void func3();
};
```

클래스 안에 가상함수가 하나라도 존재하면 그 클래스에 대한 가상함수 테이블이 만들어진다

<img src="https://data.develog.develrocket.com/upload/develog/muchan/1794914785122-u7xfjl/image.png" alt="" width="525" height="319">

이러면 Child에서 fun3은 호출되면 바로 실행되는데

가상함수는 해당 객체가 무슨 데이터형인지 판단하고 이후에 함수가 호출되므로 약간의 오버헤드가 발생하는 것이다

## 순수 가상 함수와 추상 클래스

```
class Animal {
 public:
  Animal() {}
  virtual ~Animal() {}
  virtual void speak() = 0;
};

```

가상 함수에 =0; 을 붙여서 이 함수는 반드시 오버라이딩 되어야만 한다는 것을 알려준다

이렇게 순수 가상함수가 있으면 Animal에 대한 객체를 생성하지 못하고

이렇게 순수 가상 함수가 하나 포함되어 있는, 반드시 상속되어야하는 클래스를 추상 클래스라고 한다

## 다중 상속

C++은 C#과 다르게 다중 상속을 지원한다

```
#include <iostream>

class A {
 public:
  int a;

  A() { std::cout << "A 생성자 호출" << std::endl; }
};

class B {
 public:
  int b;

  B() { std::cout << "B 생성자 호출" << std::endl; }
};

class C : public A, public B {
 public:
  int c;

  C() : A(), B() { std::cout << "C 생성자 호출" << std::endl; }
};
int main() { C c; }
```

이처럼 사용할 수 있고, 생성자는 

```
class C : public A, public B
```

상속되는 순서에 따라 호출된다고 한다

### 다이아몬드 상속

<img src="https://data.develog.develrocket.com/upload/develog/muchan/1794915452189-142vh8/image.png" alt="" width="410" height="294">

이런 구조를 다이아몬드 상속 구조라고 한다

문제가 되는 이유는 HandsomeHuman, SmartHuman이 Human을 상속받아

Human에 name이라는 멤버가 있다면 Me는 name이라는 멤버를 두개 상속받게 되는 것이다

#### 해결

```
class Human {
 public:
  // ...
};
class HandsomeHuman : public virtual Human {
  // ...
};
class SmartHuman : public virtual Human {
  // ...
};
class Me : public HandsomeHuman, public SmartHuman {
  // ...
};
```

해결은 단순히 virtual을 붙이면 된다
