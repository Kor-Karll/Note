> ## CHAPTER 02 동작 파라미터화 코드 전달하기

* 동작 파라미터화(behavior parameterization)

```
예> 

1단계 녹색사과를 모두 찾고 싶음
2단계 150그램이상인 사과를 모두 찾고 싶음
3단계 150그램 이상이면서 녹색인 사과를 모두 찾고 싶음

위와 같이 시시각각 변하는 사용자 요구사항에 대응하기 위해 동작 파라미터화를 이용하면 효과적으로 대응가능

- 동작 파라미터화란? 아직은 어떻게 실행할 것인지 결정하지 않은 코드 블록을 의미

예>

* 리스트의 모든 요소에 대해서 '어떤 동작'을 수행할 수 있음
* 리스트 관련 작업을 끝낸 다음에 '어떤 다른 동작'을 수행할 수 있음
* 에러가 발생하면 '정해진 어떤 다른 동작'을 수행할 수 있음
```

* 2.1 변화하는 요구사항에 대응하기

    * 첫번째 시도 : 녹색 사과 필터링
    ```java
    enum Color { RED , GREEN }

    public static List<Apple> filterGreenApples(List<Apple> inventory) {
        List<Apple> result = new ArrayList<>();
        for (Apple apple : inventory) { 
            if (apple.getColor() == Color.GREEN) {
                result.add(apple);
            }
        }
        return result;
    }
    ```

    고객이 변심하여 빨간 사과도 필터링하고 싶어진다면?

    * 두번째 시도 : 색을 파라미터화
    ```java
    public static List<Apple> filterApplesByColor(List<Apple> inventory, Color color) {
        List<Apple> result = new ArrayList<>();
        for (Apple apple : inventory) {
            if (apple.getColor() == color) {
                result.add(apple);
            }
        }
        return result;
    }

    List<Apple> greenApples = filterApplesByColor(inventory, Color.GREEN);
    List<Apple> redApples   = filterApplesByColor(inventory, Color.RED);
    ```

    무게를 고려하고 필터링하고 싶다면? 위와 같이 무게를 인자로 받는 방식으로 구현이 가능하지만..

* 2.2 동작 파라미터화
    * 세번째 시도 : 선택 조건을 결정하는 인터페이스를 정의(프레디케이트)

        
    ```java
    // 전략 디자인 패턴

    interface ApplePredicate { // 알고리즘 패밀리
        boolean test(Apple a);
    }

    static class AppleWeightPredicate implements ApplePredicate { // 전략
        @Override
        public boolean test(Apple apple) {
            return apple.getWeight() > 150;
        }
    }

    static class AppleColorPredicate implements ApplePredicate { // 전략
        @Override
        public boolean test(Apple apple) {
            return apple.getColor() == Color.GREEN;
        }
    }
    ```

    * 네번째 시도 : 추상적 조건으로 필터링

    ```java
    public static List<Apple> filter(List<Apple> inventory, ApplePredicate p) {
        List<Apple> result = new ArrayList<>();
        for (Apple apple : inventory) {
            if (p.test(apple)) {
                result.add(apple);
            }
        }
        return result;
    }
    ```

* 2.3 복잡한 과정 간소화

    * 익명 클래스

    * 다섯번째 시도 : 익명 클래스 사용

    ```java
    List<Apple> redApples2 = filter(inventory, new ApplePredicate() {
        @Override
        public boolean test(Apple a) {
            return a.getColor() == Color.RED;
        }
    });
    ```

    * 여섯번째 시도 : 람다 표현식 사용
    ```java
    List<Apple> result = filterApples(inventory, (Apple apple) -> Color.RED == apple.getColor());
    ```

    * 일곱번째 시도 : 리스트 형식으로 추상화
    ```java
    public interface Predicate<T> {
        boolean test(T t);
    }

    public static <T> List<T> filter(List <T> list, Predicate<T> p) {
        List<T> result = new ArrayList<>();
        for(T e: list) {
            if(p.test(e)) {
                result.add(e);
            }
        }
        return result;
    }

    // 빨간 사과
    List<Apple> redApples = filter(inventory, (Apple apple)->Color.RED == apple.getColor());
    // 2의 배수
    List<Integer> evenNumbers = filter(numbers, (Integer i)->i % 2 == 0);
    ```

* 2.4 실전 예제

    * Comparator 로 정렬하기
    ```java
    public interface Comparator<T> {
        int compare(T o1,T o2);
    }

    // 무게가 적은 순서로 목록 정렬
    inventory.sort(new Comparator<Apple>() {    // 익명클래스
        pulbic int compare(Apple a1, Apple a2) {
            return a1.getWeight().compareTo(a2.getWeight());
        }
    });

    inventory.sort((Apple a1, Apple a2)-> a1.getWeight().compareTo(a2.getWeight())); // 람다형식
    ```

    * Runnable 로 코드 블록 실행하기
    ```java
    public interface Runnable { void run();} // java.lang.Runnable

    Thread t = new Thread(new Runnalbe() {
        pulic void run() {
            System.out.println("Hello World");
        }
    });

    Thread t = new Thread(()-> System.out.println("Hello World")); // 람다형식
    ```

    * GUI 이벤트 처리하기
    ```java
    Button button = new Button("Send");
    button.setOnAction(new EventHandler<ActionEvent>(){
        public void handler(ActionEvent event) {
            label.setText("Sent!!");
        }
    });

    button.setOnAction((ActionEvent evnet) -> label.setText("Sent!!")); // 람다 표현식
    ```