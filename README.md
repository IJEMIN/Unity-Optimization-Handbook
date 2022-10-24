# Unity-Optimization-Handbook
유니티 최적화 핸드북 입니다. 당분간은 한국어로만 쓰여짐.


## LINQ

### Any() 보단 Count나 Length를 쓰자.
Any() 메서드는 컬렉션 타입으로부터 Enumerator를 생성하고 MoveNext() 헀을때 true를 반환하는지 검사합니다.
이예 따라 Count나 Length를 바로 검사하는 것보다 오버헤드가 더 발생합니다.

단, 프로퍼티로 제공되는 Count가 아니라 Linq 확장 메서드 Count()를 쓰면 비용이 더 발생하니 주의합니다.