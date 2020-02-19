> ## CHAPTER 05 스트림 활용

* 5.1 필터링

    * 프레디케이트로 필터링
    ```java
    List<Dish> vegetarianMenu = menu.stream()
                                .filter(Dish::isVegetarian)
                                .collect(toList());
    ```

    * 고유 요소 필터링
    ```java
    List<Integer> numbers = Arrays.asList(1,2,1,3,3,2,4);
    numbers.stream()
           .filter(i-> i % 2 = =0)
           .distinct()              // 중복제거
           .forEach(System.out::println);
    ```

* 5.2 스트림 슬라이싱

    * 예제
    ```java
    // 요리 목록
    List<Dish> specialMenu = Arrays.asList(
        new Dish("seasonal fruit",  true,    120, Dish.Type.OTHER),
        new Dish("prawns",          false,   300, Dish.Type.FISH),
        new Dish("rice",            true,    350, Dish.Type.OTHER),
        new Dish("chicken",         false,   400, Dish.Type.MEAT),
        new Dish("french fries",    true,    530, Dish.Type.OTHER)
    );
    ```

    * 프레디케이트를 이용한 슬라이싱
    ```java
    // takeWhile 활용
    // filter 를 이용하면 전체 스트림을 반복
    List<Dish> slicedMenu1 = specialMenu.stream()
                                        .takeWhile(dish -> dish.getCalories() < 320)
                                        .collect(toList());
    // dropWhile 활용
    // takeWhile 정반대 작업 (거짓이 되는 지점까지 발견된 요소를 버림)
    List<Dish> slicedMenu2 = specialMenu.stream()
                                        .dropWhile(dish -> dish.getCalories() < 320)
                                        .collect(toList());
    ```

    * 스트림 축소 (limit)
    ```java
    List<Dish> dishes = specialMenu.stream()
                                    .filter(dish -> dish.getCalories() > 300)
                                    .limit(3)
                                    .collect(toList());
    ```

    * 요소 건너뛰기 (skip)
    ```java
    List<Dish> dishes = specialMenu.stream()
                                    .filter(dish -> dish.getCalories() > 300)
                                    .skip(2)
                                    .collect(toList());
    ```

* 5.3 매핑

    * 스트림의 각 요소에 함수 적용하기
    ```java
    // 요리 이름
    List<String> dishNames = menu.stream()
                                 .map(Dish::getName)
                                 .collect(toList());
    // 문자열 길이
    List<String> words = Arrays.asList("Modern","Java","In","Action");
    List<Integer> wordLengths = words.stream()
                                     .map(String::length)
                                     .collect(toList());
    // 요리이름의 문자열 길이 추출
    List<Integer> dishNameLengths = menu.stream()
                                        .map(Dish::getName)
                                        .map(String::length)
                                        .collect(toList());
    ```

    * 스트림 평면화
    ```java
    // map을 이용해서 단어리스트에서 고유문자를 찾는데 실패한 예
    words.stream()
         .map(word -> word.split(""))
         .distinct()
         .collect(toList());

    // Arrays.stream() 활용(List<Stream<String>> 이 리턴, 실패)
    words.stream()
         .map(word -> word.split(""))
         .map(Arrays::stream)       // 각 배열을 별도의 스트림으로 생성
         .distinct()
         .collect(toList());

    // flatMap 사용
    List<String> uniqueCharacters =
        words.stream()
         .map(word -> word.split(""))
         .flatMap(Arrays::stream)   // 생성된 스트림을 하나의 스트림으로 평면화
         .distinct()
         .collect(toList());
    ```
    
* 5.4 검색과 매칭

    * 프레디케이트가 적어도 한 요소와 일치하는지 확인
    ```java
    if(menu.stream().anyMatch(Dish::isVegetarian)) {
        System.out.println("The menu is (some what) vegetarian friendly!!");
    }
    // anyMatch 는 boolean 을 반환하므로 최종 연산
    ```

    * 프레디케이트가 모든 요소와 일치하는지 검사
    ```java
    // allMatch
    boolean isHealthy = menu.stream()
                            .allMatch(dish -> dish.getCalories() < 1000);

    // noneMatch ( allMatch 의 반대 기능 )
    boolean isHealthy = menu.stream()
                            .noneMatch(dish -> dish.getCalories() >= 1000);
    ```

    * 쇼트서킷 평가

        전체 스트림을 처리하지 않았더라도 결과를 반환(allMatch,noneMatch,findFirst,findAny,limit...)

    * 첫 번째 요소 찾기
    ```java
    // 제3으로 나누어떨어지는 첫번째 제곱값
    List<Integer> someNumbers = Arrays.asList(1,2,3,4,5);
    Optional<Integer> firstSquareDivisibleByThree =
        someNumbers.stream()
                   .map(n -> n * n)
                   .filter(n -> n % 3 == 0)
                   .findFirst(); // 9
    ```

* 5.5 리듀싱

    * 리듀싱 연산(폴드) : 모든 스트림 요소를 처리해서 값으로 도출

    ```java
    int sum = 0;
    for ( int x : numbers ) {
        sum += x;
    }

    ==>
    // reduce는 두개의 인수를 갖는다
    // 초깃값
    // 두 요소를 조합해서 새로운 값을 만드는 BinaryOperator<T>
    int sum = numbers.stream()
                     .reduce(0, (a,b) -> a + b);

    ==>

    int sum = numbers.stream()
                     .reduce(0, Integer::sum);

    int product = numbers.stream()
                         .reduce(1, (a,b) -> a * b);
    ```

    * 최댓값, 최솟값
    ```java
    // 최댓값
    Optional<Integer> max = numbers.stream()
                                   .reduce(Integer::max);

    // 최솟값
    Optional<Integer> max = numbers.stream()
                                   .reduce(Integer::min);
    ```

* 5.6 실전 연습

    * 2011년에 일어난 모든 트랜잭션을 찾아서 값을 오름차순으로 정렬
    ```java
    List<Transaction> tr2011 = transactions.stream()
                                           .filter(transaction -> transaction.getYear() == 2011)
                                           .sorted(comparing(Transaction::getValue))
                                           .collect(toList());
    ```

    * 거래자가 근무하는 모든 도시를 중복없이 나열
    ```java
    List<String> cities = transactions.stream()
                                      .map(transaction -> transaction.getTrader().getCity())
                                      .distinct()
                                      .collect(toList());
    ```
* 5.7 숫자형 스트림

    * 기본형 특화 스트림 (IntStream,DoubleStream...)
    ```java
    // 숫자 스트림으로 매핑
    int calories = menu.stream()
                       .mapToInt(Dish::getCalories)
                       .sum();

    // 객체 스트림으로 복원
    IntStream intStream = menu.stream().mapToInt(Dish::getCalories);
    Stream<Integer> stream = intStream.boxed();

    // 기본값 : OptionalInt
    OptionalInt maxCalories = menu.stream()
                                  .mapToInt(Dish::getCalories)
                                  .max();
    int max = maxCalories.orElse(1);    // 값이 없을 때 기본 최댓값을 명시적으로 설정

    // 숫자범위
    IntStream evenNumbers = IntStream.rangeClosed(1,100)        // [1,100]의 범위
                                     .filter(n -> n % 2 == 0);  // 짝수
    ```
* 5.8 스트림 만들기

    * 값으로 스트림 만들기
    ```java
    Stream<String> stream = Stream.of("Modern","Java","In","Action");
    // 모든 문자열 대문자로 변환후 하나씩 출력
    stream.map(String::toUpperCase).forEach(System.out::println);   

    Stream<String> emptyStream = Stream.empty(); // 스트림 비우기
    ```

    * null이 될 수 있는 객체로 스트림 만들기(ofNullable)
    ```java
    Stream<String> values =
        Stream.of("config","home","user")
            .filatMap(key -> Stream.ofNullable(System.getProperty(key)));

    ```

    * 배열로 스트림 만들기(Arrays.stream)
    ```java
    int[] numbers = {2,3,5,7,11,13};
    int sum = Arrays.stream(numbers).sum(); // 합계 41
    ```

    * 파일로 스트림 만들기
    ```java
    // 파일에 고유한 단어수 세기
    long uniqueWords = 0;
    try(Stream<String> lines = 
            Files.lines(Paths.get("data.txt"), Charset.defaultCharset())){
        uniqueWords = lines.flatMap(line -> Arrays.stream(line.split(" ")))
                           .distinct()
                           .count();
    }
    catch(IOException e){
        // 파일을 열다가 예외가 발생하면 처리
    }
    ```

    * 함수로 무한 스트림 만들기

        * iterate 메서드
        ```java
        Stream.iterate(0, n -> n + 2) // 무한 스티림(언바운드 스트림)
              .limit(10)
              .forEach(System.out::println);

        IntStream.iterate(0, n -> n < 100, n -> n + 4) // 프레디케이트 지원
                 .forEach(System.out::println);

        IntStream.iterate(0, n -> n + 4)
                 .takeWhile(n -> n < 100>)
                 .forEach(System.out::println);
        ```

        * generate 메서드
        ```java
        // generate 는 Supplier<T>를 인수로 받아 새로운값 생성
        Stream.generate(Math::random)
              .limit(5)
              .forEach(System.out::println);

        // IntStream의 generate는 IntSupplier 인수
        IntStream ones = IntStream.generate(() -> 1);
        ```