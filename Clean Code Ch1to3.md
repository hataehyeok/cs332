# Clean Code

### Ch1. Clean Code

Clean Code란 : Code that strives for simplicity

- 처음에는 빠르게 코드를 작성하더라도 이후 유지보수를 위해서는 리팩터링을 진행해야 한다
- Invariant, 추상화 등으로 안전하고 깨끗한 코드로 유지한다
- 리팩터링 되지 않은 코드 → 생산성을 떨어트린다
- 에러 핸들링, 메모리 누수, 네이밍 등을 신경쓰며 작성한다
- 하나의 추상화 수준에서 여러 일을 실행 → 명확한 목적을 갖고 한 가지 일만 실행하도록 한다
- 명백한 추상화 수준을 지킨다

### Ch2. Meaningful Names

<aside>
💡 변수, 함수, 클래스 등의 이름에서 해당 코드가 어떤 역할을 수행하는지 명확하게 표시할 수 있어야 한다. 네이밍이 잘 되면 주석을 달 필요가 없어진다

</aside>

```cpp
// Bad
int d;

// Good
int elapsedTimeInDays;
int daysSinceCreation;
int daysSinceModification;
int fileAgeInDays;

// Bad
// 실제 자료구조가 List가 아닌 이상 혼동이 될 수 있음
int accountList

// Good
int accountGroup;
int bunchOfAccounts;
int accounts;

```

- Name → NameString
- money → moneyAmount
- message → theMessage

Class 이름

⇒ 명사, 명사구 위주로 사용하고 동사는 사용하지 않는다

Method 이름

⇒ 동사 혹은 동사구로 사용

⇒ getter, setter, 조건 확인 메서드는 get, set, is를 붙인다

위와 같이 구성하면, 클래스에서 메서드를 호출할 때, 명사 + 동사 구조로 코드가 작성되어 읽기 편한 코드를 작성할 수 있다

유명한 알고리즘을 사용하는 메서드의 경우 Solution Domain 으로 네이밍을 해도 된다

### Ch3. Functions

- 함수를 만들 때 최대한 짧게 만들자 ⇒ (Five lines of code 느낌)
- 한 가지 함수는 한 가지 일을 하도록
    - 한 함수에서 여러 기능을 수행한다면 함수들을 두개로 쪼갤 수 있음
    - 하나의 일만 해야 네이밍의 명확성도 높아짐
- 코드에서 추상화 수준을 맞춰야 한다
    - 어떤 함수에서 객체를 인자로 받아오거나 넘기는 일을 하는 동시에 변수로 받은 객체의 메서드를 호출하면 추상화 수준이 깨진다
- 이상적인 인수 개수는 0개이고 차선은 1개
- 플래그 인수(bool 값을 넘기는 함수)는 없는게 나음
- try/catch 구문은 원래 코드에서 분리해서 처리하는게 깔끔하고 읽기에도 편하다
- 루프 안에서 break나 continue를 사용하는 것도 별로 좋지는 않다
