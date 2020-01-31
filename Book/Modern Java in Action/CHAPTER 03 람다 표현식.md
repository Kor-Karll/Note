## CHAPTER 03 람다 표현식

* 3.1 람다란 무엇인가?

    * 람다 표현식은 메서드로 전달할 수 있는 익명 함수를 단순화한 것
    * 파라미터 리스트,바디,반환 형식,발생할 수 있는 예외 리스트
    * 익명,함수,전달(메서드 인수로 전달하거나 변수로 저장가능),간결성

    ```java
    (String s) -> s.length(); // String 형식의 파라미터, int 를 반환
    (Apple a) -> a.getWeight() > 150; // Apple 형식 파라미터, boolean 반환
    (int x, int y) -> { // int 형식의 두개의 파라미터, 리턴값 없음, void 리턴
        System.out.println("Result:");
        System.out.println(x + y);
    }
    () -> 42 // 파라미터가 없으며 42 반환
    (Apple a1, Apple a2) -> a1.getWeight().compareTo(a2.getWeight());
    ```

* 3.2 어디에, 어떻게 람다를 사용할까?

    * 함수형 인터페이스
        * 추상 메서드 한개일 경우

    ```java
    public interface Predicate<T> {
        boolean test(T t);
    }

    public interface Comparator<T> {int compare(T o1, T o2);}
    public interface Runnable {void run();}
    public interface ActionListener extends EventListener{void actionPerformed(ActionEvent e);}
    public interface Callale<V> {V call() throws Exception;}
    public interface PrivilegedAction<T> {T run();}
    ```

    * 유효한 람다 표현식은?

    ```java
    1.
    execute(() -> {});
    public void execute(Runnable r) {
        r.run();
    }

    2. 
    public Callable<String> fetch() {
        return () -> "Tricky example ;-)";
    }

    3.
    Predicate<Apple> p = (Apple a) -> a.getWeight(); // 시그니처 일치X
    ```

* 3.3 람다 활용 : 실행 어라운드 패턴

    * 초기화/준비 -> 작업 -> 정리/마무리 (순환 패턴)

    ```java
    예>
    public String processFile() throws IOException {
        try (BufferedReader br = new BufferedReader(new FileReader("data.txt"))) {
            return br.readLine();
        }
    }
    ```

    * 1단계 : 동작 파라미터화를 기억하라
    ```java
    // 두 행을 출력하고 싶은경우
    String result = processFile((BufferdReader br) -> br.readLine() + br.readLine());
    ```
    * 2단계 : 함수형 인터페이스를 이용해서 동작 전달
    ```java
    @FunctionInterface
    public interface BufferedReaderProcess {
        String process(BufferdReader b) throws IOException;
    }

    public String processFile(BufferdReaderProcess p) throws IOException {
        ...
    }
    ```

    * 3단계 : 동작 실행
    ```java
    public String processFile(BufferedReaderProcess p) throws IOException {
        try (BufferedReader br = new BufferedReader(new FileReader("data.txt"))) {
            return p.process(br);
        }
    }

    * 4단계 : 람다 전달
    ```java
    // 한행 출력
    String oneLine = processFile((BufferedReader br) -> br.readLine());
    // 두행 출력
    String twoLine = processFile((BufferedReader br) -> br.readLine() + br.readLine());
    ```

* 3.4 함수형 인터페이스 사용

    * 함수 디스크립터(function descriptor) - 함수형 인터페이스의 추상 메서드 시그니처

    * Predicate(boolean 리턴)
    ```java
    java.util.function.Predicate<T> 인터페이스
    - test 라는 추상 메서드를 정의하며 test는 제네릭 형식 T의 객체를 인수로 받아 boolean을 반환한다

    예제>
    @FunctionInterFace
    public interface Predicate<T> {
        boolean test(T t);
    }
    public <T> List<T> filter(List<T> list, Predicate<T> p) {
        List<T> results = new ArrayList<>();
        for(T t : list) {
            if(p.test(t)) {
                results.add(t);
            }
        }
        return results;
    }
    Predicate<String> nonEmptyStringPredicate = (String s) -> !s.isEmpty();
    List<String> nonEmplty = filter(listOfStrings, nonEmptyStringPredicate);
    ```

    * Consumer(void 리턴)
    ```java
    java.util.function.Consumer<T> 인터페이스
    - 제네릭 형식 T 객체를 받아서 void를 반환하는 accept라는 추상 메서드를 정의

    예제>
    @FunctionInterFace
    public interface Consumer<T> {
        void accept(T t);
    }
    public <T> void forEach(List<T> list, Consumer<T> c) {
        for(T t : list) {
            c.accept(t);
        }
    }

    forEach(
        Arrays.asList(1,2,3,4,5),
        (Integer i) -> System.out.println(i)
    );
    ```

    * Function(제네릭 형식R 리턴)
    ```java
    java.util.function.Function<T, R> 인터페이스
    - 제네릭 형식 T를 인수로 받아서 제네릭 형식 R 객체를 반환하는 추상 메서드 apply를 정의

    예제>
    @FunctionInterFace
    public interface FunctionInterFace<T, R> {
        R apply(T t);
    }
    public <T, R> List<R> map(List<T> list, Function<T, R> f) {
        List<R> result = new Array<>();
        for(T t : list) {
            result.add(f.apply(t));
        }
        return result;
    }

    List<Integer> l = map(Arrays.asList("lambdas","in","action"),
                            (String s) -> s.length()
    );
    ```

    * 기본형 특화
    ```java
    참조형 reference type (Byte, Integer, Object, List 등)
    기본형 primitive type (int,double,byte,char 등)

    박싱(boxing)        기본형 -> 참조형 변환 기능 
    언박싱(unboxing)    참조형 -> 기본형 변환 기능
    ```
    |사용 사례|람다 예제|대응하는 함수형 인터페이스|
    |---|---|---|
    |Boolean 표현|(List<String> list) -> list.isEmpty()|Predicate<List<String>>|
    |객체 생성|() -> new Apple(10)|Supplier<Apple>|
    |객체에서 소비|(Apple a) -> System.out.println(a.getWeight())|Consumer<Apple>|
    |객체에서 선택/추출|(String s) -> s.length()|Function<String,Integer> 또는 ToIntFunction<String>|
    |두 값 조합|(int a, int b) -> a * b|IntBinaryOperator|
    |두 객체 비교|(Apple a1, Apple a2) -> a1.getWeight().compareTo(a2.getWeight())|Comparator<Apple> 또는 BiFunction<Apple, Apple, Integer> 또는 ToIntBiFunction<Apple, Apple>|

* 3.5 형식 검사, 형식 추론, 제약

    * 형식 추론
    ```java
    // 형식을 추론 하지 않음
    Comparator<Apple> c = (Apple a1, Apple a2) -> a1.getWeight().compareTo(a2.getWeight());
    // 형식을 추론함
    Comparator<Apple> c = (a1, a2) -> a1.getWeight().compareTo(a2.getWeight());
    ```

    * 지역 변수 사용
    ```java
    int portNumber = 1337;
    Runnable r = () -> System.out.println(portNumber);
    
    portNumber = 31337; 
    // 에러: 람다에서 참고하는 지역변수는 final로 선언되거나 실질적으로 final 처럼 취급해야함
    ```

* 3.6 메서드 참조

* 3.7 람다, 메서드 참조 활용하기
* 3.8 람다 표현식을 조합할 수 있는 유용한 메서드
* 3.9 비슷한 수학적 개념
* 3.10 마치며

