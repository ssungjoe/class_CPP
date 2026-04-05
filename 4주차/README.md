# Chapter 05. 예외 처리하기

<br>

## 1. C++ 예외 처리 구문 (`try`, `catch`, `throw`)

C언어에서는 함수의 반환값이나 에러 코드로 예외를 처리해야 했지만, C++은 프로그램의 비정상적인 종료를 막고 오류를 우아하게 처리하기 위한 전용 구문을 제공한다.

- **`try`** : 예외가 발생할 가능성이 있는 코드 블록을 중괄호 `{}`로 감싼다.
- **`throw`** : 예외(오류) 상황이 발생했을 때, 예외 값을 `catch` 블록으로 던진다. **`throw`가 실행되는 즉시 해당 블록의 나머지 코드는 무시되고 `catch`로 점프한다.**
- **`catch`** : `throw`로 던져진 예외를 받아 오류를 처리한다. 던져진 예외의 '데이터 형식(타입)'에 맞는 `catch` 블록이 실행된다.

```cpp
#include <iostream>
using namespace std;

int main() {
    try {
        // 예외가 발생할 수 있는 코드
        cout << "try 블록 실행 중..." << endl;
        throw 1; // 정수형 1을 예외로 던짐 (여기서 즉시 점프)
        cout << "이 코드는 실행되지 않습니다." << endl;
    }
    catch (int a) { // 정수 형식 예외 받기
        cout << "catch 블록 실행: " << a << endl;
    }

    return 0;
}
```

<br>

## 2. 다중 catch 문과 만능 catch (`...`)

- 하나의 `try` 블록에서 상황에 따라 다른 형식(정수, 실수, 문자열 등)의 예외를 던질 수 있으며, 이에 대응하기 위해 **여러 개의 `catch` 블록을 연달아 작성**할 수 있다.
- **`catch (...)` (줄임표 사용)** : 던져진 예외의 자료형이 무엇이든 상관없이 **모든 예외를 다 잡을 수 있는 만능 구문**이다. 단, 다중 `catch` 문을 구성할 때 **반드시 맨 마지막에 위치**해야만 컴파일 오류가 나지 않는다.

```cpp
#include <iostream>
using namespace std;

int main() {
    try {
        throw 1.2f; // float 형식 예외 발생
    }
    catch (int a) {
        cout << "정수 예외 처리: " << a << endl;
    }
    catch (float b) {
        cout << "실수 예외 처리: " << b << endl; // 이 블록이 실행됨
    }
    catch (...) {
        // int, float 이외의 알 수 없는 모든 예외가 오면 이곳에서 처리됨
        // 주의: 반드시 맨 마지막에 작성해야 함!
        cout << "알 수 없는 예외 처리" << endl;
    }
    return 0;
}
```

<br>

## 3. 함수에서의 예외 처리와 스택 풀기 (Stack Unwinding)

- 호출된 함수 내부에서 `throw`로 예외를 발생시켰지만 해당 함수 안에 `try-catch`가 없다면, 예외는 **함수를 호출한 외부(caller)로 전달**된다.
- 프로그램은 적절한 `catch`를 찾을 때까지 호출 스택(Call Stack)을 거슬러 올라가며 예외를 계속 던진다. 이를 **스택 풀기**라고 한다. 끝까지 못 찾으면 프로그램은 비정상 종료된다.

```cpp
#include <iostream>
using namespace std;

void divide(int a, int b) {
    if (b == 0) {
        throw b; // 함수 내부에서 예외 발생
    }
    cout << "나눗셈 결과: " << a / b << endl;
}

int main() {
    try {
        divide(10, 0); // 함수 호출
    }
    catch (int e) {
        // 함수 안에서 던진 예외를 메인 함수에서 받아서 처리함
        cout << "에러: 0으로 나눌 수 없습니다. (예외 값: " << e << ")" << endl;
    }
    return 0;
}
```

<br>

## 4. 단언문 (`assert`)을 활용한 예외 처리

- `<cassert>` 헤더를 포함하여 사용한다.
- `assert(조건식)` : 조건식이 참(true)이면 무사히 넘어가지만, **거짓(0, false)이면 그 즉시 프로그램을 강제 중단**시키고 디버그 메시지(오류가 발생한 파일 경로, 줄 번호, 실패한 조건 등)를 띄운다.
- **컴파일 모드에 따른 차이 (중요)** : `assert`는 개발 단계에서 논리적 오류를 쉽게 잡기 위해 사용한다. 배포를 위한 **릴리즈(Release) 모드로 컴파일하거나 매크로에 `#define NDEBUG`를 선언하면, 컴파일러는 모든 `assert` 코드를 통째로 무시(실행 안 함)하므로** 성능 저하가 전혀 발생하지 않는다.

```cpp
#include <iostream>
#include <cassert> // assert 사용을 위한 헤더
using namespace std;

int main() {
    int number = 0;
    cout << "양수를 입력하세요: ";
    cin >> number;

    // number가 0 이하이면 여기서 프로그램 강제 종료 및 디버그 창 알림
    assert(number > 0);

    cout << "입력한 숫자: " << number << endl;
    return 0;
}
```

<br>

## 5. 모던 C++의 예외 처리 생략 (`noexcept`)과 실패 대응 (`set_terminate`)

- **`noexcept` 키워드**
  - 함수 선언이나 정의 뒤에 붙여, **"이 함수는 절대 예외를 발생시키지 않는다"**고 컴파일러에게 보증하는 키워드이다.
  - 컴파일러는 예외 처리에 필요한 내부 오버헤드를 생략하여 코드를 더 최적화할 수 있다.
  - 만약 `noexcept`로 선언된 함수 내부에서 예외가 발생해 밖으로 새어 나오면, C++ 런타임은 `std::terminate()` 함수를 호출해 프로그램을 즉시 강제 종료해버린다.

- **`set_terminate`로 예외 처리 실패 대응하기**
  - `<exception>` 헤더를 포함해야 한다.
  - `catch`로 잡지 못한 예외가 발생하거나 `noexcept` 제약이 깨졌을 때 기본적으로 실행되는 `std::terminate()` 대신, **사용자가 직접 정의한 함수가 실행되도록 가로채는 기능**이다.
  - 콜백으로 지정할 함수는 반드시 **매개변수가 없고 반환형이 `void`**여야 한다.

```cpp
#include <iostream>
#include <exception> // set_terminate 사용을 위한 헤더
using namespace std;

// 사용자가 정의한 종료 콜백 함수
void myterminate() {
    cout << "myterminate called : 예외 처리에 실패하여 강제 종료 프로세스를 밟습니다." << endl;
    exit(-1);
}

int main() {
    // 프로그램에서 처리되지 않은 예외가 발생하면 myterminate 함수를 호출하도록 설정
    set_terminate(myterminate);

    // 이 예외를 받을 catch 문이 없으므로 myterminate가 호출됨
    throw 1;

    // 이 아래 코드는 실행되지 않음
    return 0;
}
```

<br>

## 6. 예외 처리 시 주의사항: 메모리 누수 (Memory Leak)

동적 메모리(`new`)를 할당한 후 예외(`throw`)가 발생하면, 메모리를 해제하는 `delete` 구문이 실행되지 않고 `catch` 블록으로 바로 점프하기 때문에 메모리 누수가 발생한다. 이를 막으려면 `catch` 블록 내에서도 메모리를 해제해주거나 모던 C++의 스마트 포인터를 적극적으로 활용해야 한다.

```cpp
#include <iostream>
using namespace std;

int* ptr_int = nullptr;

int main() {
    try {
        int input_size = 5;
        ptr_int = new int[input_size]; // 동적 메모리 할당

        throw 1; // 강제로 예외 발생. 아래의 delete를 건너뛰고 점프함!

        delete[] ptr_int; // 원래 여기서 해제되어야 하나, 실행되지 못함.
    }
    catch (int a) {
        cout << "예외 발생! 메모리를 해제합니다." << endl;
        // 메모리 누수를 방지하기 위해 예외 처리 블록 내부에서도 할당 해제를 명시해야 함
        if (ptr_int != nullptr) {
            delete[] ptr_int;
        }
    }
    return 0;
}
```
