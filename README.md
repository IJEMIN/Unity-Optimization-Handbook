# Unity-Optimization-Handbook
유니티 최적화 핸드북 입니다. 당분간은 한국어로만 쓰여짐.


## 게임 오브젝트

### 어떤 트랜스폼을 다른 트랜스폼의 부모(Parenting)로 지정하는 것을 자제하기
유니티는 트랜스폼 연산을 최적화 하기위해 각 루트 트랜스폼들을 캐싱한다.
그런데 수많은 트랜스폼을 소수의 트랜스폼의 자식으로 넣는건 이러한 최적화의 효과를 누르지 못하게 한다. 게다가 트랜스폼 변화는 자식들에게 전파되므로, 복잡하고 깊은 부모-자식 관계는 연산량이 많아진다.

따라서 꼭 어떤 게임 오브젝트의 자식으로 들어갈 필요가 없는 경우, 패런팅 하지 않는다.



## 할당

### 로그 통제
문자열 string 생성은 할당을 발생시킵니다. 따라서 Debug.Log() 등 로그 함수에 문자열을 전달하는 과정에서 할당이 발생합니다.
플레이어 설정에서 로그 출력 여부를 조정할 수 있지만, 이는 로그 메서드의 입력에 문자열을 전달하면서 발생하는 할당을 제거하는 효과는 없습니다.

다만 개발 편의상 로그는 반드시 필요하기 때문에,  릴리즈 빌드에서 출력할 로그 수준을 결정한 다음, Conditional 전처리기로 묶어 랩핑한 버전의 Log() 메서드를 구현해서 사용합니다.
예를 들어 Log와 Warning 단계만 릴리즈 빌드에서 걷어낼 것이라면 다음과 같이 랩핑한 메서드를 만들어 사용할 수 있습니다.

아래 메서드들은 유니티 에디터(UNITY_EDITOR) 또는 개발자용 디버그 심볼(DEVELOPMENT_BUILD)이 활성화된 빌드에서만 존재하며, 이외의 경우 스트립되어 사라집니다.
따라서 해당 메서드들을 부르면서 생기는 오버헤드가 제거됩니다.

```
[Conditional("UNITY_EDITOR"), Conditional("DEVELOPMENT_BUILD")]
public static void Log(string log, Object context)
{
    UnityEngine.Debug.Log(log, context);
}

[Conditional("UNITY_EDITOR"), Conditional("DEVELOPMENT_BUILD")]
public static void LogWarning(string log, Object context)
{
    UnityEngine.Debug.LogWarning(log, context);
}
```


## LINQ

### Any() 보단 Count나 Length를 쓰자.
Any() 메서드는 컬렉션 타입으로부터 Enumerator를 생성하고 MoveNext() 헀을때 true를 반환하는지 검사합니다.
이예 따라 Count나 Length를 바로 검사하는 것보다 오버헤드가 더 발생합니다.

단, 프로퍼티로 제공되는 Count가 아니라 Linq 확장 메서드 Count()를 쓰면 비용이 더 발생하니 주의합니다.

## 미신

### TryGetComponent 가 성능이 더 좋다.
공식 문서에 언급되는 장점은 에디터에서 할당이 발생하지 않는 것이다. 하지만 플레이어 빌드에서의 성능 변화는 언급되어 있지 않다.
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