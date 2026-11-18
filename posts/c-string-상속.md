# [C++] string / 상속

> 디벨로그에서 쓴 글 · 2026-11-16
>
> https://develog.develrocket.com/@muchan/c-string-상속

[https://modoocode.com/209](https://modoocode.com/209)

위 사이트에서 공부한 내용 정리입니다

---

## 표준 string 클래스

C언어는 문자열을 쓰기 불편하다(예를들어 문자열의 길이를 한 번에 알 수 없다고 한다)

#### C++에 구현된 표준 string 클래스 특징

- 짧은 문자열에 대해서 동적으로 메모리를 할당하지 않고 그냥 지역변수로 보관한다
- 문자열을 복사할 때 복사된 문자열의 내용이 바뀌지 않는 한 실제로 문자열을 복사하는 게 아니라 가리키게 한다.

### 예제 코드

```
#include <iostream>
#include <string>

int main() {
  std::string s = "abc";
  std::string t = "def";
  std::string s2 = s;

   std::cout << s << " 의 길이 : " << s.length() <<  std::endl;
   std::cout << s << " 뒤에 " << t << " 를 붙이면 : " << s + t <<  std::endl;

  if (s == s2) {
     std::cout << s << " 와 " << s2 << " 는 같다 " <<  std::endl;
  }
  if (s != t) {
     std::cout << s << " 와 " << t << " 는 다르다 " <<  std::endl;
  }
  return 0;
}
```

그래서 위의 코드처럼 +연산과 ==, != 연산을 할 수 있고 length()를 통해 문자열의 길이도 쉽게 알 수 있다

> <=, >=와 같은 비교 연산도 가능하며, length()말고 insert(), erase(), replace()등 여러 함수들이 존재한다

## 상속

C++에서는 보통 기반-파생 클래스라고 부른다. 왜냐하면 C++은 여러 부모 클래스를 가질 수 있기 때문이다  
(부모가 여러명이면 좀 이상해서 그렇다고 한다)

```
class Base {
  std::string s;

 public:
  Base() : s("기반") {  std::cout << "기반 클래스" <<  std::endl; }

  void what() {  std::cout << s <<  std::endl; }
};

class Derived : public Base {
  std::string s;

 public:
  Derived() : Base(), s("파생") {
     std::cout << "파생 클래스" <<  std::endl;

    // Base 에서 what() 을 물려 받았으므로
    // Derived 에서 당연히 호출 가능하다
    what();
  }
};
```

여기서 Base가 기반 클래스, Derived가 파생 클래스다

<img src="https://data.develog.develrocket.com/upload/develog/muchan/1794827329463-09o389/image.png" alt="" width="436" height="295">

현재 위의 코드 구조이다.

Derived 생성자 안에서 호출하는 what은 기반 클래스에게 물려받은 함수이므로 실행할 수 있는것이다

#### 파생 클래스의 생성자

```
Derived() : Base(), s("파생")
```

파생클래스의 생성자는 기반 클래스의 생성자가 먼저 호출된 후 처리되어야해서 

기반 클래스의 생성자를 먼저 호출해야 한다

> 기반 클래스의 생성자를 명시적으로 호출하지 않으면 기반 클래스의 디폴트 생성자가 실행된다

### 오버라이딩

```
class Derived : public Base {
  std::string s;

 public:
  Derived() : Base(), s("파생") {
     std::cout << "파생 클래스" <<  std::endl;

    // Base 에서 what() 을 물려 받았으므로
    // Derived 에서 당연히 호출 가능하다
    what();
  }

  void what() {  std::cout << s <<  std::endl; }
};
```

기존에 기반 클래스에 있던 what을 Derived에서 재정의 해주면 Derived의 생성자에서 호출한 what은

기반 클래스의 what()이 아닌 파생 클래스의 what()이 호출된다

### Protected

상속 받는 클래스에서는 접근 가능하고, 그 외의 기타 정보는 접근 불가능한 접근 지정자

### 상속 받을 때 키워드

```
class Derived : public Base
```

여기 public Base에서 public은 상속 받는 클래스에서 기반 클래스의 멤버들이

실제로 어떻게 동작하는 지 알려주는 것이다

- 만일 위처럼 **public** 형태로 상속 하였다면 기반 클래스의 접근 지시자들에 영향 없이 그대로 작동한다. 즉 파생 클래스 입장에서 **public** 은 그대로 **public** 이고, **protected** 는 그대로 **protected** 이고, **private** 은 그대로 **private** 이다.
- 만일 **protected** 로 상속하였다면 파생 클래스 입장에서 **public** 은 **protected** 로 바뀌고 나머지는 그대로 유지된다.
- 만일 **private** 으로 상속하였다면 파생 클래스 입장에서 모든 접근 지시자들이 **private** 가 된다.

&nbsp;
