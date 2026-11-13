# [C++] friend / 이항 연산자 변환

> 디벨로그에서 쓴 글 · 2026-11-13
>
> https://develog.develrocket.com/@muchan/c-friend-이항-연산자-변환

[https://modoocode.com/203](https://modoocode.com/203)

위 사이트에서 공부한 내용 정리입니다

---

## friend 키워드

friend 키워드는 클래스 내부에서 다른 클래스나 함수를 friend로 정의할 수 있다. friend로 정의된 클래스나 함수들은 원래 클래스의 private으로 정의된 변수나 함수들에 접근할 수 있다

### 코드

```
class A {
 private:
  void private_func() {}
  int private_num;

  // B 는 A 의 친구!
  friend class B;

  // func 은 A 의 친구!
  friend void func();
};

class B {
 public:
  void b() {
    A a;

    // 비록 private 함수의 필드들이지만 친구이기 때문에 접근 가능하다.
    a.private_func();
    a.private_num = 2;
  }
};

void func() {
  A a;

  // 비록 private 함수의 필드들이지만 위와 마찬가지로 친구이기 때문에 접근
  // 가능하다.
  a.private_func();
  a.private_num = 2;
}

int main() {}
```

여기서 클래스A안에 클래스 B와 void 함수 func를 friend로 선언했는데, 이러면 B와 func 내부에서 A의 모든 private 멤버 변수들과 멤버 함수들에 접근 권한을 가질 수 있다

하지만 이건 단방향으로만 적용되는 것이다

A클래스 내부에서 friend class B를 선언하면 B클래스 내부에서 A의 private멤버들을 사용할 수 있는거지  
A클래스 내부에서 B의 private 멤버 변수들을 사용할 수 있는건 아니다

## 이항 연산자 변환

어떤 임의의 연산자 @에 대해 a@b라는 연산이 있다면

```
*a.operator@(b);
*operator@(a, b);
```

컴파일러는 위의 코드 두개 중 하나로 변환해서 실행한다

#### 여기서 중요한점은

*a.operator@(b)는 a의 클래스 멤버함수로서 실행되는 것이고*  
operator@(a,b)는 클래스 외부에서 정의되어 있는 일반적인 함수를 의미하는 것이다

이전에 작성한 코드에서 

```
"-1.1 + i3.923" + a;
```

해당 덧셈을 오버로딩 할 수 없어서 

```
operator+(Complex("-1.1 + i3.923"), a);
```

이런식으로 구현했다(operator+라는 외부 함수를 따로 정의해줘야함)

### operator+를 어떻게 정의해야하나

```
*operator@(a, b);
```

이렇게 실행되도록 하려면 operator+를 외부함수로 선언해야하는데

Complex 내부에 real과 img 변수는 private이라 외부 함수는 해당 변수들에 접근할 수 없다.

이럴때 friend 키워드를 사용해서 Complex의 private 변수에 접근할 수 있게 해주는 것이다

```
class Complex {
 private:
  double real, img;

  double get_number(const char* str, int from, int to) const;

 public:
  Complex(double real, double img) : real(real), img(img) {}
  Complex(const Complex& c) { real = c.real, img = c.img; }
  Complex(const char* str);

  Complex operator+(const Complex& c) const;
  Complex& operator=(const Complex& c);

  // 나머지 연산자 함수들은 생략 :)

  void println() {
    std::cout << "( " << real << " , " << img << " ) " << std::endl;
  }

  // 이제 이 함수는 Complex 의 private 멤버 변수들에 접근할 수 있습니다.
  friend Complex operator+(const Complex& a, const Complex& b);
};

Complex operator+(const Complex& a, const Complex& b) {
  Complex temp(a.real + b.real, a.img + b.img);
  return temp;
}
```

처음엔 헷갈렸는데 일단 연산자 오버로딩에서 멤버함수는 인자를 하나밖에 못받는다

그래서 두개를 받으려면 외부함수로 선언해야하는거고 그래서 꼭 friend 키워드가 필요한거다

## 새로운 문제점

위의 코드에서 메인을 아래처럼 작성하면 컴파일 에러가 발생한다

```
int main() {
  Complex a(0, 0);
  a = "-1.1 + i3.923" + a;

  a = a + a;
  a.println();
}
```

```
test.cc: In function ‘int main()’:
test.cc:106:11: warning: ISO C++ says that these are ambiguous, even though the worst conversion for the first is better than the worst conversion for the second:
  106 |   a = a + a;
      |           ^
test.cc:27:9: note: candidate 1: ‘Complex operator+(const Complex&, const Complex&)’
   27 | Complex operator+(const Complex& a, const Complex& b) {
      |         ^~~~~~~~
test.cc:91:9: note: candidate 2: ‘Complex Complex::operator+(const Complex&) const’
   91 | Complex Complex::operator+(const Complex& c) const {
      |  
```

컴파일러가 operator+에서 

```
a.operator+(a);
operator+(a, a);
```

2가지 중 뭘 선택할 지 모르겠다는 말이다

그래서 통상적으로 +, -, *, / 는 모두 외부 함수로 선언하는 것이 원칙이다

반대로 자기 자신을 리턴하는 +=, -=는 멤버 함수로 선언하는 것이 원칙이다
