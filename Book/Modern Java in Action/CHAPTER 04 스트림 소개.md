> ## CHAPTER 04 스트림 소개

* 4.1 스트림이란 무엇인가?

    * 자바 8 API에 새로 추가된 기능
    * 스트림을 이용하면 선언형으로 컬렉션 데이터를 처리할 수 있다
    * 멀티스레드 코드를 구현하지 않아도 데이터를 투명하게 병렬로 처리가능

    ```java
    // 자바 7에서
    public static List<String> getLowCaloricDishesNamesInJava7(List<Dish> dishes) {
        List<Dish> lowCaloricDishes = new ArrayList<>();
        for (Dish d : dishes) {
            if (d.getCalories() < 400) {
                lowCaloricDishes.add(d);
            }
        }
        List<String> lowCaloricDishesName = new ArrayList<>();
        Collections.sort(lowCaloricDishes, new Comparator<Dish>() {
            @Override
            public int compare(Dish d1, Dish d2) {
                return Integer.compare(d1.getCalories(), d2.getCalories());
            }
        });

        for (Dish d : lowCaloricDishes) {
            lowCaloricDishesName.add(d.getName());
        }
        return lowCaloricDishesName;
    }

    // 자바8 스트림을 이용하면
    public static List<String> getLowCaloricDishesNamesInJava8(List<Dish> dishes) {
        return dishes.stream()
        .filter(d -> d.getCalories() < 400)
        .sorted(comparing(Dish::getCalories))
        .map(Dish::getName)
        .collect(toList());
    }
    ```
    * 스트림 API 의 특징
        * 선언형 : 더 간결하고 가독성이 좋음
        * 조립할 수 있음 : 유연성
        * 병렬화 : 성능 향상

* 4.2 스트림 시작하기

    * filter : 람다를 인수로 받아 스트림에서 특정 요소를 제외
    * map : 람다를 이용해서 한 요소를 다른 요소로 변환하거나 정보를 추출
    * limit : 정해진 개수 이상의 요소가 스트림에 저장되지 못하게 스트림 크기를 축소
    * collect : 스트림을 다른 형식으로 변환

* 4.3 스트림과 컬렉션

    * 스트림은 단 한번만 소비할 수 있다
    ```java
    List<String> title = Arrays.asList("Java8","In","Action");
    Stream<String> s = title.stream();
    s.forEach(System.out::println); // title의 각 단어를 출력
    s.forEach(System.out::println); // java.lang.IllegalStateException: 스트림이 이미 소비되었거나 닫힘
    ```
    * 외부반복 : 컬렉션 인터페이스를 사용하려면 사용자가 직접 요소를 반복
    * 내부반복 : 스트림 라이브러리

* 4.4 스트림 연산

    * 중간연산 : 연결할 수 있는 스트림 연산(filter,map,limit,...)

    |연산|반환형식|연산의 인수|함수 디스크립터|
    |---|---|---|---|
    |filter|Stream\<T>|Predicate\<T>|T -> boolean|
    |map|Stream\<R>|Function\<T, R>| T -> R|
    |limit|Stream\<T>|||
    |sorted|Stream\<T>|Comparator\<T>|(T, T) -> int|
    |distinct|Stream\<T>|||


    * 최종연산 : 스트림을 닫는 연산(collect,...)

    |연산|반환형식|목적|
    |---|---|---|
    |forEach|void|스트림의 각 요소를 소비하면서 람다를 적용|
    |count|long(generic)|스트림의 요소 개수를 반환|
    |collect||스트림의 리듀스해서 리스트,맵,정수 형식의 컬렉션을 만듬|
    
* 4.5 로드맵
* 4.6 마치며