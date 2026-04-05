# Chapter 04. 실행 흐름 제어

<br>

## 1. 조건문으로 흐름 제어 (if, switch)

프로그램 실행 중 특정 조건에 따라 코드의 실행 여부를 결정하거나 분기할 때 사용한다.

### 1-1. if, else if, else 문

- **if 문** : 조건식이 참(true)이면 중괄호 `{}` 안의 코드 블록을 실행하고, 거짓(false)이면 무시한다.
- **else 문** : if 문의 조건이 거짓일 때 실행할 특정 동작을 지정한다.
- **else if 문** : 여러 개의 다중 조건을 추가하여 실행 흐름을 확장한다. 위에서부터 순차적으로 조건을 검사하며, 참인 조건의 블록 하나만 실행하고 전체 if 구문을 빠져나간다.
- **중괄호 생략 기능** : `if`, `else if`, `else` 블록 내에 실행할 구문이 단 한 줄(하나의 구문)뿐이라면 중괄호 `{}`를 생략할 수 있다.

```cpp
#include <iostream>
using namespace std;

int main() {
    int input_number;
    cout << "정수 입력: ";
    cin >> input_number;

    if (input_number > 0) {
        cout << "입력한 수는 양수 입니다." << endl;
    }
    else if (input_number < 0) {
        cout << "입력한 수는 음수 입니다." << endl;
    }
    else {
        cout << "입력한 수는 0 입니다." << endl;
    }

    // 중괄호 생략 방식 (실행 구문이 한 줄일 때)
    if (input_number > 0) cout << "양수" << endl;
    else if (input_number < 0) cout << "음수" << endl;
    else cout << "0" << endl;

    return 0;
}
```

### 1-2. switch 문과 분기

- 다중 분기 조건이 복잡할 때 `if ~ else if` 대신 사용하여 가독성을 높일 수 있는 제어문이다.
- 주어진 변수나 표현식의 평가 결과(상수)에 따라 여러 `case` 중 하나를 선택해 실행한다.
- **break 키워드** : 해당 조건을 위한 실행을 끝내고 `switch` 문을 완전히 종료시킨다.
- **break 누락 시 주의점 (Fall-through)** : `case` 구문 마지막에 `break`를 의도적으로, 혹은 실수로 누락하면 프로그램이 강제 종료되지는 않으나 **다음 `case`의 조건 확인 없이 이어지는 모든 `case` 구문이 순차적으로 실행**되는 논리 오류가 발생한다.
- **default의 정확한 의미** : 흔히 '기본값'으로 오해하지만, 영단어 default의 본래 뜻(채무불이행, 의무를 다하지 않음)처럼 프로그래밍에서도 **"주어진 어떤 case 조건에도 해당하지 않았을 때 실행되는 예외 처리 구문"**을 의미한다.

```cpp
#include <iostream>
using namespace std;

int main() {
    int input_number = 3;

    switch (input_number) {
        case 1:
            cout << "입력한 수는 1 입니다." << endl;
            break;
        case 2:
            cout << "입력한 수는 2 입니다." << endl;
            break;
        case 3:
            cout << "입력한 수는 3 입니다." << endl;
            break; // 여기서 탈출
        default:
            cout << "입력한 수는 1~3 범위 밖입니다." << endl;
            break;
    }
    return 0;
}
```

> **[심화] if 문과 switch 문 중 어떤 것이 더 빠른가요?**
> 최신 컴파일러는 코드 최적화 기능이 뛰어나기 때문에 둘 간의 성능(기계어 코드 실행 속도) 차이는 거의 없다.
> 단일 변수를 상수 값들과 비교할 때는 가독성 측면에서 `switch`가 유리하고, 비교 연산자(`>`, `<`)나 복잡한 조건식을 사용할 때는 `if` 문을 사용하는 것이 적합하다.

<br>

## 2. 반복문으로 흐름 제어 (while, do~while, for)

조건이 참인 동안 특정 코드 블록을 여러 번 실행하도록 제어한다.

### 2-1. while 문 (진입 제어 반복문)

- 조건식이 참인 동안 코드 블록을 계속 반복한다.
- 블록에 들어가기 **전**에 먼저 조건을 검사하므로, 초기 조건이 거짓이면 단 한 번도 실행되지 않는다.
- 반복 횟수를 미리 알 수 없고, 특정 조건이 충족되는 동안 계속 반복해야 할 때 유용하다.
- **무한 루프 주의** : 반복문 내부에서 조건을 거짓으로 만들 장치(조건 업데이트)가 없으면 프로그램이 끝나지 않고 멈춰버리므로 주의해야 한다.

### 2-2. do~while 문 (종료 제어 반복문)

- `while`문과 기본적으로 같지만, **조건을 나중에 검사**한다.
- 즉, 조건의 참/거짓 여부와 상관없이 **코드 블록이 무조건 최소 1회는 실행**된다. (일단 실행해 보고 조건을 검사할 때 유용)

```cpp
// while과 do~while의 차이점 비교 예제
#include <iostream>
using namespace std;

int main() {
    int i = 0;
    // 조건이 처음부터 거짓(0 < 0)이므로 한 번도 실행되지 않음
    while (i < 0) {
        cout << "i is less than 0" << endl;
        i++;
    }

    int j = 0;
    // 조건이 거짓이어도 일단 무조건 1번은 실행됨
    do {
        cout << "j is less than 0" << endl; // 이 문장은 출력됨
        j++;
    } while (j < 0);

    return 0;
}
```

### 2-3. for 문 (진입 제어 반복문)

- 반복의 횟수가 명확하거나 배열, 데이터 범위를 순회할 때 가장 유용하고 많이 쓰이는 반복문이다.
- `for (초기화; 조건식; 증감식)`의 구조를 가져 한곳에서 반복의 규칙을 파악하기 쉽다.

```cpp
#include <iostream>
using namespace std;

int main() {
    // 0부터 시작하여 5보다 작을 때까지(0, 1, 2, 3, 4) 총 5회 반복
    for (int count = 0; count < 5; count++) {
        cout << "count: " << count << endl;
    }
    return 0;
}
```

<br>

## 3. 흐름 제어 키워드 (break, continue)

조건문이나 반복문과 결합하여 프로그램의 흐름을 강제로 조작하는 키워드. `for`, `while`, `do~while` 내부에서 모두 사용 가능하다.

- **break** : 반복문을 **완전히 중단하고 탈출**한다. (가장 가까운 반복문 1개를 빠져나감)
- **continue** : 반복문을 중단하지는 않고, **현재 회차의 나머지 아래쪽 코드를 무시한 채 즉시 다음 반복(조건 검사 또는 증감식)으로 점프**한다.

```cpp
#include <iostream>
using namespace std;

int main() {
    // break 예제: count가 5가 되면 반복문 즉시 탈출
    for (int count = 0; count < 10; count++) {
        if (count == 5) {
            cout << "break로 반복문 탈출" << endl;
            break;
        }
    }

    // continue 예제: 홀수만 출력하기 (짝수일 땐 아래 출력을 무시하고 다음으로)
    for (int count = 0; count < 10; count++) {
        if (0 == count % 2) { // 짝수이면
            continue;         // 아래의 cout을 건너뛰고 증감식(count++)으로 이동
        }
        cout << "count: " << count << endl;
    }
    return 0;
}
```

<br>

## 4. 표현식(Expression)과 구문(Statement)의 차이

프로그램 코드를 구성하는 논리적인 단위에 대한 명확한 구분이다.

- **표현식 (Expression)**
  - 하나 이상의 변수, 연산자, 리터럴을 조합해 **값을 평가하고 결과(값)를 반환**하는 코드 조각.
  - 수학의 '수식'과 같으며 반드시 결괏값이 나온다.
  - 예: `b + c`, `func()`, `1 + 2`

- **구문 (Statement)**
  - 하나 이상의 연산, 동작을 실행하는 **명령문의 집합** (흐름 제어, 변수 할당 등).
  - 컴파일러가 이해하고 실행할 수 있는 최소의 독립적인 코드 조각으로 보통 세미콜론(`;`)으로 끝난다.
  - **구문은 여러 개의 표현식을 내부에 포함하는 더 큰 단위이다.**
  - 예: `int a = 0;`, `if (a > 10) break;`
