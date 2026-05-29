# 7주차. 객체지향 프로그래밍 특징과 C++ 클래스 심화

<br>

## 1. 추상화(Abstraction)와 캡슐화(Encapsulation)

### 1-1. 추상화 (Abstraction)

현실 세계의 복잡한 대상에서 '공통된 특징은 추출'하고 '불필요한 부분은 제거'하여 간결하고 이해하기 쉬운 프로그래밍 언어 모델로 변경하는 과정이다.

* **추출 예시**: 다양한 자동차에서 '바퀴', '엔진', '사람/짐을 싣는다', '이동한다'는 공통 특성을 추출하여 '자동차 클래스'를 규정한다.
* **제거 예시**: 병원 고객 관리 앱을 만들 때, 이름/주소/휴대폰 번호는 필요하지만, 직업/취미/좋아하는 음식 같은 정보는 불필요하므로 제거한다.

### 1-2. 캡슐화 (Encapsulation)와 은닉화 (Hiding)

변수와 함수를 클래스로 감싸서 외부에서 직접 접근할 수 없도록 **은닉화**하고, 외부에는 오직 대표 함수(인터페이스)만 노출하는 기법이다.

* **응집도(Cohesion)와 결합도(Coupling)**: 캡슐화를 잘 지키면 하나의 목적을 위한 기능들이 클래스 내부에 모여 **응집도가 올라가고**, 외부와는 대표 함수로만 소통하므로 클래스 간의 **결합도는 낮아진다**. 이는 내부 변경이 외부에 영향을 덜 미치게 만들어 유지보수성을 극대화한다.
* **은닉화 vs 캡슐화**: 은닉화는 불필요한 요소를 단순히 감추는 '행위 자체'를 말하며, 캡슐화는 은닉화를 통해 내부 로직을 숨기고 외부에서 간단히 사용하게 만드는 등 일련의 모든 구조적 설계를 지칭하는 더 넓은 개념이다.
* **은행 금고 비유**: 고객이 은행 금고에 직접 들어가 입출금을 하면 보안과 내역 관리에 큰 문제가 생긴다(은닉화 실패). 따라서 금고는 숨기고(`private`), 창구 직원(`public`)을 통해서만 거래하도록 만들어야 안전하다.

```cpp
class Bank {
private: 
    int safe; // 은닉화: 외부에서 절대 직접 접근 불가 (금고)

public: 
    Bank() : safe(0) {}
    // 캡슐화: 외부와 소통하는 유일한 창구 (대표 함수)
    void use_counter(int in, int out) {
        safe += in;
        safe -= out;
    }
};

int main() {
    Bank my_bank;
    // my_bank.safe = 100;  // 에러! private 변수에 직접 접근 불가
    my_bank.use_counter(100, 0); // 정상 작동: 창구를 통해 입금
}
```

### 1-3. 접근 지정자 (Access Specifiers)

* **`private`**: 클래스 내의 멤버 함수에서만 접근 가능. UML 클래스 다이어그램에서는 **`-`** 기호로 표기한다.
* **`protected`**: 클래스 내부 및 상속 관계의 자식 클래스에서 접근 가능. UML에서는 **`#`** 기호로 표기한다.
* **`public`**: 다른 클래스 어디서든 접근 가능. UML에서는 **`+`** 기호로 표기한다.



> ⚠️ **주의 및 Q&A**
> * **클래스와 구조체의 차이**: 클래스(`class`)는 접근 지정자를 생략하면 기본값이 `private`이지만, C++의 구조체(`struct`)는 기본값이 `public`이다.
> * **왜 주석으로 경고만 적어두면 안 될까?**: 인간은 실수하기 마련이므로 주석만으로는 안전을 보장할 수 없다. 접근 지정자를 쓰면 **컴파일 단계**에서 강제로 접근 권한 오류를 뱉어내어 런타임 버그를 원천 차단해 준다.
> 
> 

<br>

## 2. 상속성(Inheritance)과 다형성(Polymorphism)

### 2-1. 상속과 리스코프 치환 원칙 (Liskov Substitution)

이미 추상화/캡슐화된 부모 클래스의 속성을 자식 클래스가 물려받아 재사용 및 확장하는 것이다.

* **치환 (Substitution)**: 자식 클래스는 언제든 부모 클래스를 대체할 수 있어야 한다. 자식은 부모가 `public`으로 선언한 함수의 시그니처나 제약 사항을 그대로 유지해야 한다.

### 2-2. 단일 상속과 다중 상속의 주의점

* C++는 하나의 부모만 두는 단일 상속뿐만 아니라, 여러 부모를 두는 **다중 상속**도 지원한다.
* ⚠️ **접근 지정자 생략의 함정**: 다중 상속 시 클래스를 나열할 때 접근 지정자를 명시하지 않으면 **`private`으로 자동 지정**된다.

```cpp
// monster는 public 상속, character는 명시하지 않았으므로 private 상속이 되어버린다!
class monster_a : public monster, character { ... }; 
```

### 2-3. 다형성 (Polymorphism)과 오버라이딩 (Overriding)

부모에게 물려받은 동일한 함수(역할)가 자식의 고유한 특성에 따라 **다른 행동 방식으로 동작**하게 만드는 것이다.

* **유지보수성 향상**: 파일 리더기 예제처럼, 버전이 변경되어 자식 클래스가 교체되더라도 공통된 부모 포인터(인터페이스)를 사용하면 기존 코드를 수정할 필요 없이 하위 호환성을 유지할 수 있다.
* **오버라이딩 (Overriding)**: 부모 함수의 시그니처(이름, 매개변수, 반환형)를 그대로 유지하면서 자식 클래스에서 내부 로직만 재정의(기존 것 위에 올라타기)하는 것이다.
* **가상 함수 (`virtual`)**: 변수를 '부모 포인터/레퍼런스'로 업캐스팅하여 자식 객체를 담았을 때, 일반 함수를 호출하면 부모의 함수가 실행된다. **자식에서 오버라이딩한 함수가 호출되게 하려면 반드시 부모 함수의 선언에 `virtual` 키워드를 붙여야 한다**.



> 💡 **오버라이딩 vs 오버로딩 구분**
> * **오버라이딩**: 자식 클래스에서 완전히 같은 시그니처로 부모 함수를 재정의하는 것.
> * **오버로딩**: 함수 이름만 같고, 매개변수의 구성(타입이나 개수)이 다른 함수를 새롭게 쌓아 올리는(추가하는) 것.
> 
> 

```cpp
class Monster {
public:
    // virtual이 있어야 다형성이 작동함
    virtual void attack() { cout << "기본 공격!" << endl; } 
};

class BossMonster : public Monster {
public:
    // 부모의 함수를 자식 입맛에 맞게 재정의 (오버라이딩)
    void attack() override { cout << "강력한 보스 뇌전 공격!" << endl; } 
};

int main() {
    // 부모 타입의 껍데기(포인터)에 자식 객체(알맹이)를 담음 (업캐스팅)
    Monster* m = new BossMonster(); 
    m->attack(); // 출력: "강력한 보스 뇌전 공격!" (다형성 발현)
}
```

<br>

## 3. 생성자(Constructor)와 소멸자(Destructor)

객체가 생성되거나 소멸할 때 자동으로 호출되어 초기화나 뒷정리를 담당하는 특수 함수들이다.

### 3-1. 상속 관계에서의 생성자 호출 순서

다중 상속 시 생성자는 **가장 최상위 부모 $\rightarrow$ 다음 부모 $\rightarrow$ 자식 클래스** 순으로, 즉 상속받은 순서대로 차례차례 위에서 아래로 호출된다.
* *예: `monster_a`가 `monster`와 `character`를 상속받았다면, `monster` $\rightarrow$ `character` $\rightarrow$ `monster_a` 순으로 생성자가 호출된다*.

```cpp
class Character { public: Character() { cout << "1. Character 생성\n"; } };
class Monster { public: Monster() { cout << "2. Monster 생성\n"; } };

// 상속받은 순서(Monster -> Character)대로 부모부터 먼저 호출됨
class MonsterA : public Monster, public Character { 
public: 
    MonsterA() { cout << "3. MonsterA 생성\n"; } 
};

int main() { MonsterA my_monster; }
// 출력 결과: 1. Monster 생성 -> 2. Character 생성 -> 3. MonsterA 생성
```

### 3-2. ⚠️ 생성자에서 다른 생성자를 호출할 때의 치명적 실수

자식 생성자 안에서 부모나 자신의 다른 매개변수 생성자를 호출하려고 중괄호 `{}` 내부에 `monster_a(10, 10);` 처럼 코드를 적으면, 이는 기존 객체를 초기화하는 것이 아니라 **메모리에 일회성으로 쓰이고 버려지는 '이름 없는 임시 객체'를 엉뚱하게 새로 만들어버리는 행위**가 된다.
* **해결책 (초기화 목록)**: C++11부터는 이를 해결하기 위해 매개변수 뒤에 **콜론(`:`)을 붙이는 '초기화 목록(Initializer List)'** 자리에서만 다른 생성자를 명시적으로 호출(`monster_a() : monster_a(10, 10) { ... }`)해야 정상적으로 델리게이트가 작동한다.

```cpp
class MonsterA {
    int x, y;
public:
    MonsterA(int _x, int _y) : x(_x), y(_y) { cout << "매개변수 생성자\n"; }
    
    // 올바른 방법: 초기화 목록을 통해 다른 생성자 호출
    MonsterA() : MonsterA(10, 10) { 
        cout << "기본 생성자\n"; 
    }
};
```

### 3-3. 멤버 변수 초기화의 4가지 종류

1. **직접 초기화 (Direct)**: 초기화 목록을 사용해 `location(0, 0)` 형태로 직접 값을 넣는다.
2. **복사 초기화 (Copy)**: 생성자 바디 안에서 대입 연산자(`=`)를 쓴다. Rvalue가 필요해 메모리와 성능이 약간 낭비된다.
3. **유니폼 초기화 (Uniform)**: C++11 문법으로 배열 등을 `data_queue{0, 1, 2}` 형태로 초기화한다.
4. **⚠️ 필수 초기화 목록 대상**: 변경 불가능한 **상수(`const`) 멤버**나 **레퍼런스(`&`) 멤버**는 생성과 동시에 값이 정해져야 하므로 생성자 바디 내부의 복사 초기화를 쓸 수 없고, **반드시 초기화 목록(`:`)에서 처리**해야 한다.

```cpp
class Player {
    const int id;     // 상수 멤버
    int& target_ref;  // 레퍼런스 멤버
    int hp;
public:
    // 상수와 레퍼런스는 중괄호 {} 내부에서 '='로 대입할 수 없음. 반드시 초기화 목록 사용!
    Player(int _id, int& _target) : id(_id), target_ref(_target), hp(100) { }
};
```

### 3-4. 정적 멤버 변수 (Static Member) 초기화

`static` 변수는 모든 객체가 공유하므로, 객체가 생성되기 전인 프로그램 시작 시점에 메모리에 할당되어야 한다.
* 따라서 선언은 클래스 안에 하더라도, **초기화는 반드시 클래스 범위 밖의 전역(`.cpp` 파일 최상단)에서 단 한 번만 수행**해야 한다 (`int monster_b::total_count = 0;`).

```cpp
class Monster {
public:
    static int total_count; // 클래스 내부: 선언만 함
};

// 클래스 외부 전역 범위: 메모리 할당 및 초기화 (필수)
int Monster::total_count = 0;
```


### 3-5. 얕은 복사와 깊은 복사 (복사 생성자)

객체를 다른 객체에 대입(`monster_b m2 = m1;`)할 때 기본적으로 포인터 변수의 메모리 '주소'만 단순히 베껴 쓰는 얕은 복사(Shallow Copy)가 일어난다.

* **치명적 오류**: 얕은 복사가 된 두 객체가 소멸할 때, 둘 다 같은 포인터 메모리를 해제(`delete`)하려고 시도하므로 **이중 해제(Double Free, Debug Assertion Failed)** 오류가 발생하며 프로그램이 뻗는다.
* **해결 (깊은 복사)**: 복사 생성자 `monster_b(const monster_b &ref)`를 오버라이딩하여 내부에서 새로운 메모리를 힙에 동적 할당(`new`)한 뒤, 실제 문자열 데이터 등을 복사(`strcpy_s`)하여 원본과 사본을 완전히 독립시켜야 한다.
* *참고: 메모리 복사 시 안전성을 위해 구형 `strcpy` 대신 크기를 명시하는 `strcpy_s`를 사용해야 버퍼 오버런을 막을 수 있다*.

```cpp
class Monster {
    char* name;
public:
    Monster(const char* n) { name = new char[100]; strcpy_s(name, 100, n); }
    
    // 깊은 복사를 수행하는 복사 생성자 재정의
    Monster(const Monster& ref) {
        name = new char[100]; // 사본을 위한 새로운 메모리 공간 할당
        strcpy_s(name, 100, ref.name); // 실제 문자열 값을 복사
    }
    ~Monster() { delete[] name; }
};
```

### 3-6. 소멸자와 ⚠️ 가상 소멸자 (Virtual Destructor)
소멸자는 클래스명 앞에 `~`를 붙이며, 생성의 역순으로 호출된다.

* **리스코프 치환과 가상 소멸자**: 부모 포인터로 자식 객체를 생성(`monster *m = new monster_a();`)한 뒤, `delete m;`을 호출하면 컴파일러는 포인터 타입만 보고 부모의 소멸자만 호출해버려 자식의 메모리가 유출(Leak)되는 끔찍한 현상이 발생한다.
* **해결책**: 반드시 부모 클래스의 소멸자에 **`virtual`** 키워드를 붙여 가상 소멸자로 만들어야( `virtual ~monster() { }`), 다형성이 적용되어 자식 소멸자까지 완벽하게 순차적으로 연쇄 호출된다.

```cpp
class Parent {
public:
    // virtual이 없으면 메모리 누수 발생!
    virtual ~Parent() { cout << "부모 소멸\n"; } 
};
class Child : public Parent {
public:
    ~Child() { cout << "자식 소멸\n"; }
};

int main() {
    Parent* p = new Child();
    delete p; // virtual 덕분에 "자식 소멸" -> "부모 소멸" 순으로 정상 호출됨
}
```

<br>

## 4. 자신을 가리키는 `this` 포인터

객체들이 생성될 때 멤버 '변수'는 각자의 메모리(스택 등) 공간을 갖지만, 멤버 '함수'는 메모리에 한 번만 올라가고 모든 객체가 이를 공유한다.

* 컴파일러는 이 공유 함수가 도대체 어떤 객체의 변수를 건드려야 할지 알기 위해, 호출될 때 몰래 **객체 자신의 메모리 주소인 `this` 포인터**를 넘겨준다.



### 4-1. `this`의 주요 활용 방안

1. **매개변수와 멤버 변수 구별**: 매개변수 이름과 클래스의 멤버 변수 이름이 같아 충돌할 때, `this->safe = safe;`처럼 명시하여 구별한다.
2. **멤버 함수 체이닝 (Method Chaining)**: 함수 내부 연산이 끝난 후 자기 자신의 참조(`return *this;`)를 반환하게 설계(`bank&` 반환형)하면, `bank.deposit(10).withdraw(5)`처럼 꼬리에 꼬리를 무는 직관적인 연속 호출이 가능해진다.

```cpp
class Bank {
    int safe = 0;
public:
    // 반환형을 자기 자신의 레퍼런스(Bank&)로 설정
    Bank& deposit(int safe) {
        this->safe += safe; // 1. this-> 로 멤버 변수와 매개변수 구별
        return *this;       // 2. 작업 후 자기 자신을 반환
    }
};

int main() {
    Bank b;
    // 반환된 객체를 바탕으로 다시 함수를 연속 호출 (메소드 체이닝)
    b.deposit(10).deposit(20).deposit(30); 
}
```

<br>

## 5. 함수 오버로딩과 연산자 오버로딩

### 5-1. 함수 오버로딩 (Function Overloading)

* **개념**: 완전히 **같은 이름을 가진 함수를 여러 개 정의**하되, 전달받는 **매개변수의 구성(개수나 자료형)을 다르게** 하여 마치 다른 함수처럼 동작하게 만드는 문법이다.
* **의미**: 기존 함수를 지우는 것이 아니라, 새로운 정의를 계속 쌓아 올린다(Over-load)는 의미를 가진다.
* **오버라이딩(Overriding)과의 차이**:
    * 오버라이딩은 부모에게 물려받은 함수를 자식이 '덮어쓰는(재정의)' 것이다.
    * 오버로딩은 같은 클래스 내에서 이름만 같고 재료(매개변수)가 다른 함수를 '여러 개 추가'하는 것이다.


같은 `move`라는 이름의 함수지만, 하나는 `x, y` 좌표 1개만 받고, 다른 하나는 배열을 통째로 받아 여러 번 이동하게끔 매개변수를 다르게 구성할 수 있다.

```cpp
class character {
protected:
    int location[2];
public:
    // 1번 오버로딩: x, y 좌표 1개만 전달받음
    void move(int x, int y) {
        location[0] = x; location[1] = y;
    }

    // 2번 오버로딩: 이동할 x, y 배열과 횟수를 통째로 전달받음
    void move(int x[], int y[], int spot_count) {
        for (int i = 0; i < spot_count; ++i) {
            location[0] = x[i]; location[1] = y[i];
        }
    }
};

int main() {
    character obj;
    int x_list[3] = {10, 15, 20}; int y_list[3] = {10, 15, 20};
    
    obj.move(10, 10); // 1번 move 함수가 알아서 호출됨
    obj.move(x_list, y_list, 3); // 2번 move 함수가 알아서 호출됨
}

```

### 5-2. 연산자 오버로딩 (Operator Overloading)

객체 간에는 기본적으로 `+`, `-`, `*`, `/` 같은 산술 연산자를 쓸 수 없다. 하지만 **함수 오버로딩의 원리를 이용**하여, 이 기호들이 객체를 만났을 때 어떻게 동작할지 프로그래머가 직접 재정의할 수 있다.

* **원리**: 연산자 오버로딩도 결국 함수 오버로딩과 완전히 똑같은 개념이다. 단지 함수 이름이 `operator+` 처럼 특수하게 생겼고, 사용할 때 `+` 기호만 쓴다는 점이 다를 뿐이다.
* **문법**: `반환_형식 operator연산자_기호(매개변수)`

```cpp
class monster_c {
    int level;
public:
    void set_level(int l) { level = l; }
    int get_level() { return level; }

    // 덧셈 연산자(+) 기호를 만나면 실행될 함수를 오버로딩
    monster_c operator+(monster_c &operand) {
        monster_c result;
        // 두 몬스터의 레벨을 더해서 새로운 몬스터에 세팅
        result.set_level(this->level + operand.get_level());
        return result;
    }
};

int main() {
    monster_c m1, m2;
    m1.set_level(1); m2.set_level(2);
    
    // 컴파일러가 m1 + m2를 m1.operator+(m2) 로 알아서 해석하여 실행함!
    monster_c new_m = m1 + m2; // new_m의 레벨은 3이 됨
}

```

<br>

## 6. 접근 지정자와 프렌드 (Friend)

### 6-1. ⚠️ 상속에 따른 접근 지정자의 변화 매트릭스

상속받을 때 지정한 키워드(`public`, `protected`, `private`)에 따라, 부모에게 물려받은 멤버들이 자식 클래스 내부에서 어떤 접근 권한으로 변질되는지 규정한다.

* **`public` 상속**: 부모의 권한을 그대로 가져온다 (가장 흔함).
* **`protected` 상속**: 부모의 `public` 멤버들이 자식 내에서는 `protected`로 등급이 강등된다.
* **`private` 상속**: 부모의 `public`과 `protected` 멤버들이 모조리 가장 폐쇄적인 `private`으로 강등된다.

```cpp
class Parent {
public: int pub_data;
protected: int pro_data;
};

// protected 상속: Parent의 public 멤버가 Child 내부에서는 protected 취급됨
class Child : protected Parent { 
    void test() { pub_data = 10; } // 내부에서는 사용 가능
};
// Child c; c.pub_data = 10; // 에러! 밖에서는 접근 불가
```

### 6-2. 정보 은닉을 깨는 특별 권한: 프렌드 (Friend)

`private` 멤버는 원칙적으로 남이 볼 수 없지만, 예외적으로 **특정 외부 함수나 클래스를 친구(`friend`)로 등록해 주면 내 모든 비밀 데이터에 접근하도록 합법적으로 문을 열어준다**.

* **프렌드 클래스**: 내 클래스 안에 `friend class automobile;`을 적어두면, 저 클래스 전체에서 내 private 변수에 자유롭게 접근한다.
* **프렌드 함수**: 특정 함수의 원형만 적어 (`friend void break_system::pushing_break(accelerator&);`), 그 함수 하나에만 접근 권한을 허가한다.
* ⚠️ **주의사항 (은닉화의 파괴자)**: 프렌드 속성은 **상속되지 않는다** (부모의 친구라고 해서 자식의 친구인 것은 아니다). 또한 캡슐화와 은닉화 원칙을 정면으로 파괴하는 행위이므로, 연산자 오버로딩 등 불가피한 경우가 아니면 남용하지 않는 것이 좋다.

```cpp
class Engine {
private:
    int power = 100;
    // Accelerator 클래스에게 내 private 영역의 프리패스 권한을 줌!
    friend class Accelerator; 
};

class Accelerator {
public:
    void push(Engine& e) {
        // friend 선언 덕분에 외부 클래스인데도 private 변수에 맘대로 접근 가능
        e.power += 50; 
    }
};
```

<br>