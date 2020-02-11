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
    List<Dish> slicedMenu1 = specialMenu.stream()
                                        .takeWhile(dish -> dish.getCalories() < 320)
                                        .collect(toList());
    ```


* 5.3 매핑
* 5.4 검색과 매칭
* 5.5 리듀싱
* 5.6 실전 연습
* 5.7 숫자형 스트림
* 5.8 스트림 만들기
* 5.9 마치며