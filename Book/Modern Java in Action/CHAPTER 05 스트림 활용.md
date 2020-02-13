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
* 5.7 숫자형 스트림
* 5.8 스트림 만들기
* 5.9 마치며