# [C++] iostream 입출력

> 디벨로그에서 쓴 글 · 2026-11-18
>
> https://develog.develrocket.com/@muchan/c-iostream-입출력

[https://modoocode.com/213](https://modoocode.com/213)

위 사이트에서 공부한 내용 정리입니다

---

## C++ 입출력 라이브러리

<img src="https://data.develog.develrocket.com/upload/develog/muchan/1795002087066-hjd06j/image.png" alt="" width="421" height="402">

C++ 입출력 라이브러리는 다음과 같은 클래스 구조로 이루어져있다

모든 입출력 클래스는 ios_base 클래스를 기반 클래스로 갖고 ios_base는 입출력 형식 관련 데이터를 처리한다

> 예를 들어 10진수로 처리할지 16진수로 처리할지가 있다

## 스트림 버퍼

스트림 버퍼란 데이터를 내보내거나 받아들이기 전에 임시로 저장하는 곳이다

스트림 버퍼는 ios 클래스에서 초기화 된다

## istream 클래스

istream 클래스는 ios클래스에서 실제로 입력을 수행하는 클래스다

대표적으로 operator >> 가 있다

이전에 배웠듯이 operator>>를 상속 받아서 입력을 받는 연산을 오버로딩 할 수 있다

```
istream& operator>>(istream& in, std::string& s)

{
  // 구현한다
}
```

이런 느낌으로 구현하면 된다

### operator >> 특징

모든 공백문자 (띄어쓰기나 엔터, 탭 등)을 입력시에 무시해버린다

#### 코드

```
#include <iostream>
#include <string>

int main() {
  std::string s;
  while (true) {
    std::cin >> s;
    std::cout << "word : " << s << std::endl;
  }
}
```

#### 실행 결과

```
this is a long sentence
word : this
word : is
word : a
word : long
word : sentence
ABCD EFGH IJKL
word : ABCD
word : EFGH
word : IJKL
```

공백문자에 따라서 각각을 분리하여 입력 받으므로 위와 같은 결과가 나오는 것이다

## 스트림을 관리하는 flag

- goodbit : 스트림에 입출력 작업이 가능할 때
- badbit : 스트림에 복구 불가능한 오류 발생시
- failbit : 스트림에 복구 가능한 오류 발생시
- eofbit : 입력 작업시에 [EOF](https://modoocode.com/167) 도달시

&nbsp;
