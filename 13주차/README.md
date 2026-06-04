# 13주차. 객체지향 설계 원칙 (SOLID)

## 0. SOLID 원칙이란?

* 2000년대 초반, 로버트 C. 마틴(Robert C. Martin)이 발표한 객체지향 설계의 다섯 가지 핵심 원칙이다.
* 마이클 C. 페더스(Michael C. Feathers)가 개발자들이 부르기 쉽도록 각 원칙의 앞 글자를 따서 **SOLID**라는 머리글자로 소개했다.
* **구성 요소**:
1. **S**RP (단일 책임 원칙)
2. **O**CP (개방·폐쇄 원칙)
3. **L**SP (리스코프 치환 원칙)
4. **I**SP (인터페이스 분리 원칙)
5. **D**IP (의존성 역전 원칙)



## 1. 단일 책임 원칙 (SRP, Single Responsibility Principle)

### 1-1. 개념과 산탄총 수술(Shotgun Surgery)

* **정의**: "클래스는 오직 한 가지 기능만 수행해야 하고, **클래스를 변경해야 하는 이유는 단 하나**여야 한다"는 원칙이다.
* **산탄총 수술의 경고**: 거대 클래스가 여러 책임을 떠맡고 있으면, 기능 하나를 수정할 때 연관된 여러 클래스를 동시다발적으로 뜯어고쳐야 한다. 총을 쏘면 탄흔이 사방에 남듯 코드 수정의 파급력이 흩어지는 이 끔찍한 현상을 '산탄총 수술(Shotgun Surgery)'이라고 부르며, 유지보수성을 극도로 떨어뜨린다.

### 1-2. 해결책: 클래스 추출과 컴포지션/어그리게이션

다중 상속으로 얽힌 거대 클래스(Large Class)를 작은 단위의 클래스들로 분할(추출)해야 한다. 그 후, 분할된 클래스들을 다중 상속이 아닌 **컴포지션(Composition)이나 어그리게이션(Aggregation) 같은 'has-a' 관계**로 포함시켜 논리적 관계를 유지해야 결합도를 낮출 수 있다.

> 💡 **다중 상속을 아예 안 쓰는 게 좋지 않나요?**
> <br>다중 상속을 남발하면 클래스가 뚱뚱해져 '산탄총 수술'이 발생하기 쉽다. 하지만 상속 관계가 단순하고 깊지 않을 때는 오히려 구현을 간단하게 만들어 주므로, 무조건 배척하기보단 **"필요할 때 한정해서 신중하게"** 사용하는 것이 바람직하다.

**[C++ 예제: SRP 위반과 준수]**

```cpp
// SRP 위반: Monster 클래스가 '전투 로직'과 '길찾기 알고리즘' 두 가지 책임을 모두 가짐.
class BadMonster {
public:
    void attack() { /* 공격 로직 */ }
    void find_route() { /* A* 알고리즘 길찾기 로직 */ } 
};

// SRP 준수: 책임을 분리하고 '컴포지션'으로 조립함.
class RouteFinder { // 길찾기 책임만 전담
public:
    void find_route() { /* A* 알고리즘 길찾기 로직 */ }
};

class GoodMonster {
private:
    RouteFinder router; // 어그리게이션/컴포지션 활용 (has-a)
public:
    void attack() { /* 공격 로직 */ }
    void move() { router.find_route(); } // 길찾기는 전문가에게 위임
};

```

## 2. 개방·폐쇄 원칙 (OCP, Open-Closed Principle)

### 2-1. 개념

* **정의**: "소프트웨어 개체는 **확장에는 열려(Open) 있어야 하고, 수정에는 닫혀(Closed) 있어야 한다**."
* **의미**: 새로운 기능(연말 특별 커피, 새로운 몬스터 등)을 언제든 쉽게 추가할 수 있어야 하며, 기능이 추가된다고 해서 기존의 핵심 코드(고객과 점원의 주문 방식 등)를 뜯어고쳐서는 안 된다는 뜻이다. 동적 바인딩(가상 함수)이 이 원칙을 가장 잘 실현한다.

### 2-2. 템플릿 메서드 패턴 (Template Method Pattern)

OCP를 완벽하게 구현하는 대표적인 디자인 패턴이다.

* **원리**: 기능의 전체적인 '뼈대(흐름)'는 추상 클래스나 전역 함수에 미리 작성해 두고, 세부적인 살(구체적인 동작 알고리즘)을 붙이는 작업은 자식 클래스의 오버라이딩에 위임한다.

**[C++ 예제: OCP를 적용한 템플릿 메서드 패턴]**

```cpp
class Monster {
public:
    // 세부 동작은 자식에게 강제 위임 (확장에 열림)
    virtual void find_route() = 0;   
    virtual void attack_special() = 0; 
};

class MonsterA : public Monster {
public:
    void find_route() override { cout << "최단 거리 우선 탐색\n"; } //
    void attack_special() override { cout << "인탱글 공격\n"; } //
};

// 🌟 핵심: 템플릿 로직 (수정에 닫힘)
// 새로운 MonsterB, MonsterC가 수백 개 추가되어도 이 함수의 코드는 단 한 줄도 수정할 필요가 없다!
void monster_routine(Monster* mon) { //
    mon->find_route();
    mon->attack_special();
}

```

## 3. 리스코프 치환 원칙 (LSP, Liskov Substitution Principle)

### 3-1. 개념

* **정의**: "하위(자식) 클래스는 언제나 자신의 상위(부모) 클래스를 완벽하게 대체(치환)할 수 있어야 한다."
* 1987년 바바라 리스코프(Barbara Liskov)가 발표하여 다형성의 완벽한 동작 원리를 수학적으로 증명한 원칙이다.

### 3-2. 'is-a' 관계의 본질

단순히 문법적인 상속을 넘어서, 논리적으로 완벽한 'is-a(자식은 부모이다)' 관계가 성립해야만 LSP를 지킨 것이다.

1. 부모 클래스 포인터로 자식 객체를 담는 **업캐스팅(Upcasting)이 완벽히 동작**해야 한다.
2. 자식 클래스는 부모가 약속한 멤버 함수의 규격(시그니처)을 상속받아 오버라이딩하거나 유지해야 하며, 부모의 의도를 무시하는 엉뚱한 예외를 발생시키면 안 된다.

**[C++ 예제: 부모 포인터로 자식을 완벽히 제어 (LSP)]**

```cpp
class Terrain { //
public:
    virtual void allocate_monster() = 0; //
};

class ForestTerrain : public Terrain { //
public:
    void allocate_monster() override { cout << "숲에 몬스터 배치\n"; } //
};

class CyberTerrain : public Terrain { //
public:
    void allocate_monster() override { cout << "사이버 공간에 몬스터 배치\n"; } //
};

// 메인 로직에서는 지형이 숲이든 사이버든 신경 쓰지 않고 부모(Terrain)의 규격대로만 명령한다.
// 자식들은 이 호출을 완벽하게 치환하여 소화해 낸다.
void setup_map(Terrain* terrain) {
    terrain->allocate_monster(); //
}

```

## 4. 인터페이스 분리 원칙 (ISP, Interface Segregation Principle)

### 4-1. 개념

* **정의**: "인터페이스는 작고 섬세하게(Fine-grained) 분리되어야 하며, 클래스는 **자신이 사용하지 않는 불필요한 인터페이스(기능)를 억지로 구현하도록 강요받아서는 안 된다**."

### 4-2. SRP(단일 책임 원칙)와의 관계

* 만약 하나의 거대한 `IMonster` 인터페이스에 '이동', '공격', '마법' 기능이 다 몰려있다면 어떻게 될까? 공격 기능이 없는 일반 마을 주민(NPC) 객체를 만들 때도 억지로 '공격' 함수를 상속받아 빈 껍데기로라도 구현해야 한다.
* 이는 다중 책임을 지는 것이므로 **SRP 위반**으로 직결된다.
* **해결책**: 거대 인터페이스를 `IRoute`(이동 전담)와 `IAttack`(공격 전담) 등으로 섬세하게 쪼개야 한다. NPC는 `IRoute`만 상속받고, 전투 몬스터는 둘 다 다중 상속받게 설계한다.

**[C++ 예제: ISP 위반과 준수]**

```cpp
// ❌ [ISP 위반]: 뚱뚱한 인터페이스
class ICharacter {
public:
    virtual void walk() = 0;
    virtual void shoot_fireball() = 0; // 마법을 못 쓰는 기사는 이걸 왜 구현해야 하나?
};

// ⭕ [ISP 준수]: 작고 섬세하게 쪼개진 인터페이스
class IRoute { //
public:
    virtual void find_route() = 0; //
};

class IAttack { //
public:
    virtual void attack_target() = 0; //
};

// 몬스터는 이동과 공격이 모두 필요하므로 필요한 것만 쏙쏙 골라 상속받음
class Monster : public IRoute, public IAttack { //
    void find_route() override { /* ... */ } //
    void attack_target() override { /* ... */ } //
};

// 마을 주민은 공격할 일이 없으므로 IRoute만 상속받음 (불필요한 구현 강요 제거!)
class NPC : public IRoute { //
    void find_route() override { /* ... */ } //
};

```

## 5. 의존성 역전 원칙 (DIP, Dependency Inversion Principle)

### 5-1. 개념

* **정의**: "상위 수준의 모듈은 하위 수준의 모듈에 직접 의존해서는 안 되며, **상위와 하위 모듈 모두 '추상화된 인터페이스'에 의존**해야 한다."

### 5-2. 나쁜 설계 vs 좋은 설계 (무기와 탈것 예제)

* **DIP 위반 (구상 클래스 의존)**: `Player` 클래스(상위)가 `MachineGun` 클래스와 `Car` 클래스(하위)를 직접 멤버 변수로 가지고 있는 경우다. 만약 게임에 '활(Arrow)'이나 '말(Horse)'이 추가된다면 `Player` 클래스의 소스 코드를 뜯어고쳐야 하는 재앙이 발생한다.
* **DIP 준수 (추상 인터페이스 의존)**: `Player`는 오직 `IWeapon`과 `IRiding_object`라는 추상 인터페이스만 바라보게(의존하게) 한다. 실제 기관총이나 자동차도 이 인터페이스를 상속받아 구현한다. 무기가 수백 개 추가되어도 `Player` 클래스는 절대 수정되지 않는다.

> 💡 **SOLID 원칙은 내용이 너무 복잡한데 꼭 지켜야 하나요?**
> <br>C++ 프로그래밍을 하는 데 있어 이 원칙들이 문법적으로 필수인 것은 아니며 초보자에게는 꽤 어렵게 다가온다.
> 하지만 이 원칙들을 코드에 적용하려고 끊임없이 고민하고 연습하다 보면, 단순한 코더를 넘어 **"변경에 유연하고 튼튼한 좋은 소프트웨어 아키텍처"를 설계할 수 있는 진짜 개발자**로 성장할 수 있으므로 반드시 습관을 들이는 것이 좋다.

**[C++ 예제: DIP를 적용한 플레이어 설계]**

```cpp
// 하위 모듈(무기, 탈것)과 상위 모듈(플레이어)을 연결할 중간 다리 '추상 인터페이스'
class IWeapon { //
public:
    virtual void shoot() = 0; 
};

// 하위 모듈은 인터페이스를 상속받아 구현
class MachineGun : public IWeapon { //
public:
    void shoot() override { cout << "두두두두!\n"; }
};

class Arrow : public IWeapon { //
public:
    void shoot() override { cout << "피슝!\n"; }
};

class Player {
private:
    // 상위 모듈인 Player는 기관총이나 활 같은 구체적인(하위) 객체를 직접 모른다.
    // 오직 추상적인 인터페이스(IWeapon) 포인터에만 의존한다. (의존성 역전)
    IWeapon* my_weapon = nullptr; //

public:
    // 외부에서 만들어진 구체적인 무기를 인터페이스 포인터로 꽂아줌 (어그리게이션)
    void set_weapon(IWeapon* new_weapon) { //
        my_weapon = new_weapon; //
    }
    
    void attack() {
        if (my_weapon) my_weapon->shoot();
    }
};

```