# Unity-Optimization-Handbook
유니티 최적화 핸드북 입니다. 당분간은 한국어로만 쓰여짐.


## 게임 오브젝트

### 어떤 트랜스폼을 다른 트랜스폼의 부모(Parenting)로 지정하는 것을 자제하기
유니티는 트랜스폼 연산을 최적화 하기위해 각 루트 트랜스폼들을 캐싱한다.
그런데 수많은 트랜스폼을 소수의 트랜스폼의 자식으로 넣는건 이러한 최적화의 효과를 누르지 못하게 한다. 게다가 트랜스폼 변화는 자식들에게 전파되므로, 복잡하고 깊은 부모-자식 관계는 연산량이 많아진다.

따라서 꼭 어떤 게임 오브젝트의 자식으로 들어갈 필요가 없는 경우, 패런팅 하지 않는다.



## 할당

### TryGetComponent() 메서드 사용하기
유니티 에디터에서 TryGetComponent() 메서드는 찾으려는 타입의 컴포넌트가 게임 오브젝트에 없다면 할당을 발생시키지 않는다.

## LINQ

### Any() 보단 Count나 Length를 쓰자.
Any() 메서드는 컬렉션 타입으로부터 Enumerator를 생성하고 MoveNext() 헀을때 true를 반환하는지 검사합니다.
이예 따라 Count나 Length를 바로 검사하는 것보다 오버헤드가 더 발생합니다.

단, 프로퍼티로 제공되는 Count가 아니라 Linq 확장 메서드 Count()를 쓰면 비용이 더 발생하니 주의합니다.

## 미신

### TryGetComponent 가 성능이 더 좋다.
공식 문서에 언급되는 장점은 에디터에서 할당이 발생하지 않는 것이다.
하지만 유니티 코리아 엔지니어에게 문의한 결과, 아래 코드들은 플레이어 빌드에서 성능 차이가 없는 것으로 나온다.

```
if(!TryGetComponent<Component>(out _))
{
    Debug.Log("comp is null");
}
```

```
if(GetComponent<Component>() == null)
{
    Debug.Log("comp is null");
}
```