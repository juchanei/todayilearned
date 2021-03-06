# 4장 클래스와 인터페이스
---

## 13: 클래스와 멤버의 접근 권한은 최소화하라
### 정보은닉
- 잘 설계된 모듈은 구현 세부사항을 다른 모듈에 잘 감추느냐가 결정 = 정보은닉
- 정보 은닉은 모듈 사이의 의존성을 낮추기 때문에...
    - 병렬적으로 개발할 수 있게 한다.
    - 프로파일링을 쉽게 해 성능향상을 할 수 있다.

### 접근제어
- 정보은닉은 **접근제어** 메커니즘 으로 구현한다
    - Package-level 클래스/인터페이스 접근제어
        - **public**
            - 다른 모듈에 공개하는 클래스/인터페이스
            - public으로 선언하면 외부에 공개되는 API가 된다. 즉, 한번 공개하면 수정하기 어렵다.
        - **package-private**
            - 해당 모듈에만 공개하는 클래스/인터페이스
            - package-private로 선언하면 구현 세부사항이 된다. 즉, 클라이언트 코드를 망가뜨리지 않고 수정할 수 있다.
    - Class-level 필드/메서드/중첩클래스/중첩인터페이스 접근제어
        - **public**
        - **package-private**
            - default 접근권한자
        - **protected**
            - protected는 사실상 외부로 공개되는 API다. 상속만 받으면 공개되기 때문.
        - **private**
        - 단, Serialize를 구현하는 클래스의 private, package-private 멤버들은 공개 API 속으로 세어나갈 수 있다.
    - 상위 클래스 메서드를 재정의할 때는 원래 메서드의 접근권한보다 낮은 권한을 설정할 수 없다.

### public 필드
- 객체 필드는 절대로 public으로 선언하면 안된다.
    - 필드들은 반드시 기본 자료형 값들을 갖거나, 변경 불가능 객체를 참조해야 한다.
    - 변경 가능 public 필드를 가진 클래스는 다중 스레드에 안전하지 않다.
    - 변경 불가능 객체를 참조하는 final 필드라 해도 public으로 선언하면 (API가 되므로) 클래스의 내부 데이터 포현 형태를 유연하게 바꿀 수 없게 된다.
- 단, 상수는 public static final 필드로 선언하여 공개할 수 있다.

### public 배열
- 길이가 0이 아닌 배열은 언제나 변경 가능하다.
    - public static final 배열로 두면 안된다.
    - 배열 필들을 반환하는 접근자/게터를 정의하면 안된다.
- 배열을 필드로 사용하고자 한다면
    - 배열을 private로 바꾸고 변경 불가능한 리스트를 만들거나
    - 배열을 private로 바꾸고 복사해서 반환하는 함수를 만든다.
    
---

## 14: public 클래스 안에는 public 필드를 두지 말고 접근자 메서드를 사용하라
### 접근자 메서드
- 선언된 패키지 밖에서도 사용 가능한 클래스에는 접근자 메서드를 제공하라.
    - public 필드를 가진 클래스는 데이터필드를 직접 조작할 수 있어 캡슐화의 이점을 누릴 수 없다.
    - public 클래스의 데이터 필드를 공개하게 되면, 그 내부 표현을 변경할 수 없게 된다. 변경하면 이미 작성된 클라이언트 코드를 깨뜨리게 되기 때문.
- 하지만, package-private 클래스나 private 중첩 클래스는 데이터 필드를 공개하더라도 잘못이라 말할 수 없다. 때로는 더 이득.
> Q. 그럼 언제가 좋을까?

---

## 15: 변경 가능성을 최소화하라
### 변경 불가능 클래스
- 변경 불가능(immutable) 클래스는 그 객체를 수정할 수 없는 클래스다.
- 변경 불가능 클래스를 만들 때는 아래의 다섯 규칙을 따르면 된다.
    - 객체 상태를 변경하는 메서드(수정자 등)을 제공하지 않는다.
    - 계승(extends)할 수 없도록 한다.
    - 모든 필드를 final로 선언한다.
        - 프로그래머의 의도를 드러내기 위함.
        - 새로 생성 된 객체에 대한 참조가 동기화 없이 다른 스레드로 전달되어도 안전하다.
    - 모든 필드를 private로 선언한다.
    - 변경 가능 컴포넌트에 대한 독점적 접근권을 보장한다.
        - 클라이언트는 클래스에 포함된 변경 가능 객체에 대한 참조를 획득할 수 없어야 한다 = 방어적 복사본

### 함수형 접근법
- this의 객체를 변경하는 대신, 새로운 객체를 만들어 반환한다.
- 함수형 접근법은 변경 불가능성을 보장한다.
    - 변경 불가능 객체는 단순하다.
    - 변경 불가능 객체는 스레드에 안전하여 동기화가 필요 없다.
        - 이는 스레드 안전성을 보장하는 가장 쉬운 방법.
    - 변경 불가능 객체는 자유롭게 공유할 수 있다.
        - 자주 사용되는 객체를 public static final로 만들어 재사용할 수 있다.
        - 객체를 캐시하여 이미 있는 객체가 거듭 생성되지 않도록 정적 팩터리를 제공할 수 있다. ex) BigInteger
        - 방어적 복사본을 만들 필요가 없다.
    - 변경 불가능 객체는 그 내부도 공유할 수 있다.
        - ex) BigInteger의 negate()
    - 변경 불가능 객체는 다른 객체의 구성요소로도 훌륭하다.
        - map, set의 key로 사용할 경우, 불변식이 깨질 걱정이 없음.
- 하지만, 값마다 별도의 객체를 만들어야 하는 단점이 있다.
    - 변경 가능 동료 클래스를 사용하면 어느정도 완화 가능.
        - 단, 성능향상의 확신이 있을때만 해야한다.
        - ex) String(변경 불가능 클래스), StringBuilder(변경 가능 동료 클래스)

### 계승의 문제
- 전달된 인자가 기본 클래스인지, 하위 클래스인지 확인이 필요하다.
    - 기본 클래스가 변경 불가능하다고해서 하위 클래스도 변경 불가능하다고 단정할 수 없기 때문.
        - 메서드를 재정의하여 하위 클래스를 변경 가능하게 수정할 수 있다.
    - 변경 불가능 클래스의 모든 필드는 변경 불가능해야하지만, 위 이유로 변경 불가능을 보장할 수 없어진다.
    
---

## 16: 계승하는 대신 구성하라.
### 계승의 문제
- 객체 생성 가능 클래스(구체 클래스)라면, 해당 클래스가 속한 패키지 밖에서 계승을 시도하는 것은 위험하다.
- 계승은 캡슐화 원칙을 위반한다.
    - 상위 클래스의 구현에 의존할 수 밖에 없기 때문에, 하위 클래스는 수정된 적이 없어도 망가질 수 있다.
    - 릴리즈가 계속되면서 구현이 변경되면, 클라이언트 클래스는 깨지기 쉬운 클래스가 된다.
    - 릴리즈가 계속되면서 새로운 메서드가 추가될 수 있는데, 새로운 메서드가 하위 클래스에서 재정의하지 않은 새 메서드를 호출해서 원치 않는 일을 일으킬 수 있다.
    - 새로 추가된 메서드가 재수없게 하위 클래스의 메서드와 시그네처가 같을 경우 문제를 일으킬 수 있다.

### 대안
- 계승 대신 새로운 클래스에 기존 클래스 객체를 private 필드를 하나 둔다.
    - 이런 설계기법을 구성이라 부른다.
    - 이런 구현기법을 전달이라 부른다.
    - 이렇게 구현된 메서드를 전달메서드라 부른다.
- 장식자 패턴/wrapper 클래스를 이용한다.
    - 성능이 저하되거나, 메모리 요구량이 늘어나지 않는 것으로 판명 되었다.
    - 단, 이는 콜백 프레임워크에 사용하기엔 부적합하다. SELF 문제.
- 계승은 'IS-A'관계가 성립할 때만 한다.

---

## 17: 계승을 위한 설계와 문서를 갖추거나, 그럴 수 없다면 계승을 금지하라.
### 문서
- 재정의 가능한 메서를 내부적으로 어떻게 사용하는지 반드시 문서에 남겨야 한다.
    - 어떤 재정의 가능 메서드를 어떤 순서로 호출하는지
    - 호출 결과가 추후 어떤 영향을 미치는지
    - 그외 모든 상황
    - 관습적으로, 메서드 주석문 마지막에 '이 구현은...'으로 시작하여 명시한다.

### 계승을 위해 설계한 클래스
- 문서를 작성한다.
- 클래스 내부 동작에 개입할 수 있는 훅(hook)을 protected 메서드 형태로 제공한다.
- 계승을 위해 설계한 클래스는 릴리즈 하기 전에 직접 하위 클래스를 만들어 테스트한다.
- 생성자에서는 직/간접적으로 재정의 가능 메서드를 호출해서는 안된다.
    - 상위 클래스의 메서드보다 하위 클래스의 재정의된 메서드가 먼저 실행된다.
    - 상위 클래스의 생성자가 호출한 하위 클래스의 메서드가, 하위 클래스의 초기화에 의존한다면 문제가 발생한다.

### 요약
- 계승에 맞도록 설계하고 문서화 하지 않은 클래스에 대한 하위 클래스는 만들지 않아야 한다.
- 모든 생성자를 private이나 package-private으로 선언하고, public 정적 팩토리 메서드를 추가해 계승을 막는다.
- 클래스 내부적으로 재정의 가능 메서드를 사용하지 않도록하여 계승에 안전한 클래스를 만든다.

---
