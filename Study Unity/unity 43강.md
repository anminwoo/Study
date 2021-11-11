2Scripts에 Weapon 스크립트 만들기

Player에서 Weapon Point에 있는 무기들에 컴포넌트로 넣어준다.



### Weapon 스크립트

```c#
public class Weapon : MnonBehaviour
{
    public enum Type { Melee, Range };
    public Type type;
    public int damage;
    public float rate;
    public BoxCollider meleeArea;
    public TrailRenderer trailEffect;
    
    public void Use()
    {
        if(type == Type.Melee)
        {
            StopCoroutine("Swing");
            StartCoroutine("Swing");
        }
    }
    
    IEnumerator Swing()
    {
        yield return new WaitForSeconds(0.1f);
        meleeArea.enabled = true;
        trailEffect.enabled = true;
        
        yield return new WaitForSeconds(0.3f);
        meleeArea.enabled = false;
        
        yield return new WaitForSeconds(0.3f);
        trailEffect.enabled = false;
    }
    
}
```

Type : Melee

Damage : 원하는대로 (이 강의에서는 20으로 함)

Rate : 0.4

#### 박스 콜라이더 넣어주기

Center X : 0, Y : 2.5, Z : 0

Size X : 3.5, Y : 3, Z : 2

콜라이더를 Melee Area에 할당

Melee 태크 생성 후 할당

Is Trigger 체크



무기 안에 Create Empty -> 이름을 Effect로 해줌

#### Trail Renderer 추가

Trail Renderer : 잔상을 그려주는 컴포넌트

Material에서 Default-Line으로 설정

Width Curve 설정 오른쪽 버튼 누르면 Add key가 생기고 그걸로 설정할수 있음

Time값을 줄여 잔상 길이를 줄여준다. 0.5로 함

Min Vertex Distance로 잔상 꺾임을 설정함 1.5로 함

Color로 잔상 색상을 설정해준다.

잔상이 나올 위치를 설정해준다.

Trail Renderer와 BoxCollider를 비활성화 시켜놓는다.




#### 일반함수와 코루틴의 차이

일반함수 : 메인루틴 -> 서브루틴 -> 메인루틴 (교차 실행)

코루틴 함수 : 메인루틴 + 코루틴 (동시 실행)

IEnumerator : 열거형 함수 클래스

yield : 결과를 전달하는 키워드

yield 키워드를 여러 개 사용하여 시간차 로직을 만들수 있다.

WaitForSeconds() : 주어진 수치만큼 기다리는 함수

yield break로 코루틴 탈출 가능

StartCoroutine() : 코루틴 실행 함수

StopCoroutine() : 코루틴 정지 함수



공격 실행부터 듣기