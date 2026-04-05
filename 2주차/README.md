# Chapter 02. 변수와 연산자

<br>

## C++ 표준 입출력

- C언어의 `stdio.h`(`scanf`, `printf`) 대신 C++에서는 `<iostream>`에 정의된 `cin`, `cout` 스트림 객체를 사용한다.
- `%d`, `%f` 같은 형식 지정자나 주소 연산자(`&`) 없이도 컴파일러가 데이터 형식을 자동으로 처리한다.
- **네임스페이스 (Namespace)**
  - 변수나 함수가 속해 있는 소속을 지정하여 이름 충돌을 방지한다.
  - `using namespace std;`를 선언하면 표준 네임스페이스인 `std::`를 생략할 수 있어 코드가 간결해진다.

```cpp
#include <iostream>
using namespace std; // 네임스페이스 사용 선언

int main() {
    int i, j;
    cout << "Enter num_1: "; // 문자열 출력 (printf 대체)
    cin >> i;                // 정수 입력 (scanf 대체)

    cout << "Enter num_2: ";
    cin >> j;

    // 연속 출력 및 endl을 통한 줄 바꿈
    cout << "num_1 + num_2 = " << i + j << endl;

    return 0;
}
```

<br>

## 데이터 형식 (C언어와 다른 C++ 추가/변경 개념)

- **보이드 (`void`)와 제네릭 포인터**
  - "형식 없음"을 의미하며, 반환값이나 매개변수가 없는 함수에 사용한다.
  - C++에서는 모든 형식을 가리킬 수 있는 제네릭 포인터(`void *`) 초기화 시 `nullptr` 키워드를 사용한다.

- **불리언 (`bool`)**
  - 참(`true`: 1)과 거짓(`false`: 0)만 가질 수 있는 1byte 형식이다.

```cpp
// void 및 제네릭 포인터 사용 예제
void* ptr_value = nullptr; // C++의 안전한 널 포인터 키워드 nullptr 사용
int int_value;
ptr_value = &int_value;

// bool 사용 예제
bool is_true = true;
```

- **와이드 문자 (`wchar_t`)**
  - 1byte로 표현 불가능한 유니코드(UTF-16 등) 문자를 저장할 때 사용한다.
  - 리터럴 앞에 `L`을 붙여 와이드 문자열임을 명시하며, `wcout`으로 출력한다.

```cpp
#include <iostream>
#include <io.h>
#include <fcntl.h>
using namespace std;

int main() {
    wchar_t message_korean[] = L"반갑다 세계야!"; // L을 붙여 유니코드 문자열 선언

    // 윈도우 콘솔 창 유니코드 출력 모드 설정 (비주얼 스튜디오 전용)
    _setmode(_fileno(stdout), _O_U16TEXT);

    wcout << message_korean << endl;
    return 0;
}
```

- **부동 소수점 정밀도 제어 (`<iomanip>`, `<limits>`)**
  - 부동 소수점 리터럴 뒤에 `f`를 붙이면 `float`(4byte), 안 붙이면 기본적으로 `double`(8byte)로 취급된다.
  - 기본 출력은 유효 자릿수가 6자리로 제한되나, `<iomanip>`, `<limits>` 헤더를 이용해 데이터 형식이 표현할 수 있는 최대 자릿수까지 정밀하게 출력할 수 있다.

```cpp
#include <iostream>
#include <iomanip>
#include <limits>
using namespace std;

int main() {
    float val_float = 3.14159265358979f; // f 접미사로 float 명시
    double val_double = 3.14159265358979; // 접미사 없으면 double

    // 최대 정밀도 출력 (numeric_limits 활용)
    cout << setprecision(numeric_limits<double>::digits10 + 1) << val_double << endl;
    return 0;
}
```

<br>

## 변수의 유효 범위와 명시적 형 변환

- **변수의 유효 범위와 범위 지정 연산자 (`::`)**
  - 변수는 지역 범위(블록 내부)와 전역 범위 등에 따라 유효 범위가 나뉜다.
  - 지역 변수와 전역 변수의 이름이 같을 때, 전역 변수에 접근하려면 변수명 앞에 **범위 지정 연산자(`::`)**를 붙여야 한다.

```cpp
#include <iostream>
using namespace std;

int value = 1; // 전역 변수

int main() {
    int value = -1; // 지역 변수

    cout << value << endl;   // 지역 변수 출력 (-1)
    cout << ::value << endl; // 전역 변수 출력 (1) - 범위 지정 연산자 사용
    return 0;
}
```

- **형 변환 (Type Casting)**
  - **암시적 형 변환**: 컴파일러가 자동 수행. 큰 범위에서 작은 범위로 변환 시 데이터 유실 경고(`warning C4244`)가 발생할 수 있다.
  - **명시적 형 변환**: C언어 방식인 `(float)val` 외에도, C++에서는 함수 형태의 캐스팅인 `float(val)`을 사용할 수 있다.
  - C++은 더 안전한 목적별 캐스팅(`static_cast`, `const_cast`, `dynamic_cast`, `reinterpret_cast`) 연산자도 별도로 제공한다.

```cpp
int int_a = 10, int_b = 5;

// 암시적 변환 (소수점 유실 발생)
float float_avg_1 = (int_a + int_b) / 2;

// 명시적 형 변환 (C++ 함수형 캐스팅 방식 사용)
float float_avg_2 = float(int_a + int_b) / 2;
```

<br>

## 문자열과 사용자 정의 리터럴

- **C++ 스타일 문자열 (`std::string`)**
  - 메모리를 직접 관리하고 끝에 널 문자(`\0`)가 필요한 C언어의 `char[]` 대신, C++에 추가된 `std::string` 클래스를 사용하는 것이 편리하다.

```cpp
#include <iostream>
#include <string> // C++ 문자열 라이브러리
using namespace std;

int main() {
    string string_value("Hello");
    string_value = "World!"; // 대입 연산자로 값 덮어쓰기 가능
    cout << string_value << endl;
    return 0;
}
```

- **사용자 정의 리터럴 (User-defined Literals)**
  - `operator""` 키워드를 활용하여 사용자가 직접 단위(`_km`, `_mi` 등)에 대한 리터럴 접미사를 정의할 수 있다.

```cpp
#include <iostream>
using namespace std;

const long double km_per_mile = 1.609344L; // L 접미사는 long double

// 마일(_mi) 단위를 킬로미터로 변환하는 사용자 리터럴 정의
long double operator"" _mi(long double val) {
    return val * km_per_mile;
}

int main() {
    // 1.0_mi 리터럴을 만나면 자동으로 함수가 실행되어 km 값으로 저장됨
    long double distance = 1.0_mi;
    cout << distance << "km" << endl;
    return 0;
}
```

<br>

## 표현식, 연산자, 출력 조작자

- **단항 연산자 (`++`, `--`, `~`)와 16진수 출력 조작자 (`hex`)**
  - 전위 연산(`++a`)은 먼저 증가, 후위 연산(`a++`)은 대입 후 증가.
  - `~` 연산자는 비트열을 반전시켜 1의 보수를 만든다 (2의 보수는 여기에 +1).
  - `cout << hex`를 사용하면 이후 출력되는 숫자가 16진수로 표기된다.

```cpp
unsigned int val = 0x00000000;
val = ~val; // 비트 반전 (1의 보수)
cout << hex << val << endl; // ffffffff 출력
```

- **이항 비트 연산 (`&`, `|`, `^`)과 `<bitset>`을 활용한 2진수 출력**
  - `&`(AND), `|`(OR), `^`(XOR).
  - `<bitset>` 헤더를 포함하면 `bitset<8>(변수)` 형태로 정수를 손쉽게 8비트 2진수 문자열로 출력할 수 있다.

```cpp
#include <iostream>
#include <bitset>
using namespace std;

int main() {
    int a = 13; // 00001101
    int b = 27; // 00011011
    int c = a ^ b; // XOR 연산: 같으면 0, 다르면 1 (00010110)

    // bitset<8>을 사용하여 8자리 2진수로 출력
    cout << "c = " << bitset<8>(c) << " : " << c << endl;
    return 0;
}
```

- **논리 출력 조작자 (`boolalpha`)**
  - `cout << boolalpha`를 스트림에 넘기면, 이후 출력되는 논리 연산 결과가 `0`/`1` 대신 `false`/`true` 문자열로 출력된다.

```cpp
short num_3 = 4, answer_1 = 5;
cout << boolalpha;
// 명시적 캐스팅과 결합하여 논리값을 true/false 문자열로 출력
cout << (bool)(answer_1 & num_3) << endl;
```

- **시프트 연산 (`<<`, `>>`) 경고**
  - `>>` (산술 시프트): 음수의 경우 부호 유지를 위해 최상위 비트를 이전 부호와 같은 값으로 채운다.
  - 시프트 횟수가 음수이거나 데이터 형식의 크기(예: 32비트)를 넘어서면 **정의되지 않은 동작**이 발생하며 컴파일러가 경고(`warning C4293`)를 띄운다.

- **삼항 연산자와 연산자 우선순위 주의**
  - `조건식 ? true일때 : false일때` 구조로 `if~else` 문을 대체한다.
  - 복잡한 연산 시 우선순위가 헷갈린다면 반드시 괄호 `()`를 사용하여 명시적으로 계산 순서를 지정해야 논리적 오류를 피할 수 있다.
