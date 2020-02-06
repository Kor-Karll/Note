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

    * 

* 4.3 스트림과 컬렉션
* 4.4 스트림 연산
* 4.5 로드맵
* 4.6 마치며