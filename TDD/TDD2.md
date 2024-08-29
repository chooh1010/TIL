## TDD

### 테스트하기 힘든 코드를 테스트 가능한 구조로 만들기

```java
public void move() {
    if (getRandomNo() >= FORWARD_NUM)
        this.position++;
}

private int getRandomNo() {
    Random random = new Random();
    return random.nextInt(MAX_BOUND);
}
```

다음과 같은 코드는 랜덤값을 예측하기가 힘들기 때문에 테스트하기 힘든 코드이다.

레거시 코드를 테스트하기 위해 리팩토링하려고 할 때 메소드 시그니쳐를 변경하지 않고 어떻게 리팩토링할 수 있을까?

```java
public void move() {
    if (getRandomNo() >= FORWARD_NUM)
        this.position++;
}

protected int getRandomNo() {
    Random random = new Random();
    return random.nextInt(MAX_BOUND);
}
```

getRandomNo()메소드의 접근제한자를 protected로 바꾸면 테스트 가능한 구조로 만들 수 있다.

```java
@Test
void move() {
    Car car = new Car("pobi") {
        @Override
        protected int getRandomNo() {
            return 4;
        }
    }
    car.move();
    assertThat(car.getPosition()).isEqualTo(1);
}
```

이런식으로 테스트할 때 getRandomNo()메소드를 오버라이딩해서 원하는 값이 반환되도록 바꿀 수 있다.

물론 랜덤값을 얻는 메소드를 Object Graph에서의 상위 노드에 구현하면 랜덤값에 대한 의존관계가 없는 코드들은 테스트가 가능하다. 레거시 코드에서는 위의 단계를 거치고 점진적으로 코드를 리팩토링하는 것이 좋다.

유지보수를 하다가 move하는 조건이 자주 추가되거나 바뀌게 된다면 인터페이스로 분리해서 바뀌는 것에 맞춰서 구현할 수 있다. move메서드에 의존관계를 주입해서 유연하고 테스트하기 쉬운 코드로 바꾸는 것이다.

```java
public interface MovingStrategy {
    boolean movable();
}
```

```java
public void move(MovingStrategy movingStrategy) {
    if (movingStrategy.movable()) {
        position = position.move();
    }
}
```

```java
@Test
void move() {
    Car car = new Car("chooh");
    car.move(new MovingStrategy() {
        @Override
        public boolean movable() {
            return true;
        }
    });
    assertThat(car.getPosition()).isEqualTo(1);
}
```

인터페이스로 분리하게 되면 테스트를 위한 구현체를 만들어서 테스트할 수 있다.

### 모든 원시값과 문자열을 포장

모든 원시값과 문자열을 가지는 객체를 만들어라.

도메인 객체에서 getter메소드와 setter메소드는 가능한 쓰지않는 것이 좋다.

```java
@Test
void create() {
    Position position = new Position(3);
    assertThat(position).isEqualTo(new Position(3));
}
```

이런식으로 객체와 객체를 비교해서 getter메소드를 사용하지 않을 수 있다.

```java
public class Position {
    private final int position;

    public Position(int position) {
        if (position < 0) {
            throw new IllegalArgumentException("position은 음수 값을 가질 수 없습니다.");
        }
        this.position = position;
    }
}
```

int형으로 position이 돌아다니게 되면 음수값이 넣어져서 버그가 발생할 수 있는데 Position으로 객체를 만들면 양수값이 보장이 된다.

이렇게 분리를 하면 단일 책임의 원칙도 지키게 되어 좋은 코드가 될 수 있다.

객체의 데이터 값을 변경할 때는 데이터를 직접 꺼내지 않고 객체한테 메시지를 보내서 로직구현을 위임하는 것이 좋다.

상태 데이터를 가지는 객체가 상태 데이터에 대한 변경을 자기 자신이 컨트롤할 수 있어야 한다.

```java
public class Position {
    private final int position;

    public Position(int position) {
        if (position < 0) {
            throw new IllegalArgumentException("position은 음수 값을 가질 수 없습니다.");
        }
        this.position = position;
    }
    
    public Position move() {
        return new Position(position + 1);
    }
}
```

move메소드로 인해 객체 자신의 값이 바뀌는 것이 아니라 새로운 인스턴스를 만들어서 반환하기 때문에 불변 객체이다.

원시값을 포장한 객체를 만들면 값에 대한 유효범위도 객체가 책임을 지게 되고 불변하고 유효한 객체를 가지고 프로그래밍하면 버그가 발생할 확률이 줄어든다.

하지만 객체가 많이 만들어지기 때문에 GC가 많이 발생해서 성능 이슈가 일어날 수 있다.

### 일급 콜렉션

하나의 콜렉션만 가지는 클래스

콜렉션을 원시타입이나 문자열로 생각해서 똑같이 포장

```java
public class Cars {
    private final List<Car> cars;

    public Cars(List<Car> cars) {
        this.cars = cars;
    }

    public List<Car> findWinners() {
        return findWinners(getMaxPosition());
    }

    private List<Car> findWinners(Position maxPosition) {
        List<Car> winners = new ArrayList<>();
        for (Car car : cars) {
            if (car.isWinner(maxPosition)) {
                winners.add(car);
            }
        }
        return winners;
    }

    private Position getMaxPosition() {
        Position maxPosition = new Position(0);
        for (Car car : cars) {
            maxPosition = car.getMaxPosition(maxPosition);
        }
        return maxPosition;
    }
}
```

이런식으로 객체로 감싸면 int값이 아니라 Position으로 로직구현을 하는 것이 좋다.

객체로 감싸서 로직구현을 하면 equals로 객체를 비교할 수 있다.

```java
public static List<Car> findWinners(List<Car> cars, Position maxPosition) {
    List<Car> winners = new ArrayList<>();
    for (Car car : cars) 
        if (car.isWinner(maxPosition)) {
            winners.add(car);
        }
    }
    return winners;
}
```

위와 같이 findWinners를 static을 붙여서 클래스 메소드로 구현하면 테스트하기 쉬운 코드가 된다.

