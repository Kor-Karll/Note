> ## CHAPTER 06 스트림으로 데이터 수집

* collect와 컬렉터로 구현 예제
    * 통화별로 트랜잭션을 그룹화, 해당 통화로 일어난 모든 트랜잭션 합계를 계산(Map<Currency, Integer>반환)
    * 트랜잭션을 비싼 트랜잭션과 저렴한 트랜잭션 두 그룹으로 분류(Map<Boolean, List<Transaction>>반환)
    * 트랜잭션을 도시 등 다수준으로 그룹화, 각 트랜잭션이 비싼지 저렵한지 구분(Map<String, Map<Boolean, List<Transaction>>>반환)

```java
Map<Currency, List<Transaction>> transactionByCurrencies = new HashMap<>();
// 그룹화한 트랜잭션을 저장할 맵 생성

for(Transaction transaction : transactions) { // 트랜잭션 리스트를 반복
    Currency currency = transaction.getCurrency();
    List<Transaction> transactionForCurrency = transactionByCurrencies.get(currency);

    if(transactionForCurrency == null) { // 현재 통화를 그룹화하는 맵에 항목이 없으면 생성
        transactionForCurrency = new ArrayList<>();
        transactionByCurrencies.put(currency, transactionForCurrency);
    }
    transactionForCurrency.add(transaction); // 같은 통화를 가진 트랜잭션 리스트에 현재 탐색중인 트랜잭션 추가
}
```
* 6.1 컬렉터란 무엇인가?
* 6.2 리듀싱과 요약
* 6.3 그룹화
* 6.4 분할
* 6.5 Collector 인터페이스
* 6.6 커스텀 컬렉터를 구현해서 성능 개선하기
* 6.7 마치며