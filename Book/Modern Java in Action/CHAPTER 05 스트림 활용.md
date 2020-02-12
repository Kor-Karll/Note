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
* 5.5 리듀싱
* 5.6 실전 연습
* 5.7 숫자형 스트림
* 5.8 스트림 만들기
* 5.9 마치며