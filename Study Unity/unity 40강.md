아이템 만들기

매쉬가 포함된 오브젝트의 위치, 각도 조절하기

새 자식 오브젝트를 만들고 Light 컴포넌트를 추가

|                               |                                                              |
| ----------------------------- | ------------------------------------------------------------ |
| Type                          | 광원타입(여기서는 point 사용)                                |
| Intensity                     | 빛의 세기                                                    |
| Range                         | 빛의 범위                                                    |
| Color                         | 빛의 색상                                                    |
| Emission                      | 파티클 입자 출력양                                           |
| Shape                         | 파티클 입자 출력 모양                                        |
| Color Over Life Time          | 시간에 따른 색상변화 (중간 부분을 누르면 중단점이 생기고 거기에서 그라데이션을 넣을 수 있음.) |
| Size Over Life Time           | 시간에 따른 크기변화                                         |
| Limit Velocity over Life Time | 시간에 따른 속도 제한 (Drag는 저항값)                        |
| Start Life Time               | 입자 생존 시간 (Random Between Two Constants로 랜덤한 모양 가능) |
| Start Speed                   | 입자 시작 속도                                               |



Particle System 컴포넌트를 추가

파티클 효과의 색상이 <a style = "color: Magenta">자홍색(Magenta)</a>이라면 재질의 못찾았다는 의미이다.

Renderer에서 Material을 찾아서 연결 (여기서는 Default-Line을 사용함)

추가한 무기에 Rigidbody와 Sphere Collider * 2 추가

Sphere Collider 중 하나의 Radius를 5로 설정해준다.

**꼭 IsTrigger를 체크해준다.**



### 아이템 스크립트 생성

```c#
public class Item : MonoBehaviour
{
    public enum Type { Ammo, Coin, Grenade, Heart , Weapon };
    public Type type;
    public int value;
    
    void Update()
    {
        transform.Rotate(Vector3.up * 10 * Time.deltaTime);
    }
}
```

enum : 열거형 타입 *(타입 이름 지정 필요)*



### 아이템 만들기 (다른 무기도 동일)

권총을 하이라키창으로 드래그

Light (GameObject) 추가

Position X : 0, Y : 0.8, Z : 0

Light 컴포넌트 추가 

Range : 4

Intensity : 10



Particle (GameObject) 추가

Particle System 컴포넌트 추가

Default-Line 추가

Shape -> Rotation X : -90으로 돌려 위로 나오도록 설정해줌

Emission -> Rate over Time :  7

Color of Lifetime에서 색깔 지정해주기

Size of Lifetime 에서 원하는 그래프 고르기

Limit Velocity over Lifetime -> Drag 0.7

Start Life Time 2, 4   Random Between Two Constants 선택

Rigidbody와 Sphere Collider * 2 추가

Sphere Collider 중 하나의 Radius를 5로 설정해준다.

**꼭 IsTrigger를 체크해준다.**

Item 스크립트 추가 후 Type : Weapon, Value : 1로 설정해준다.

이렇게 다른 아이템들 (탄약, 하트, 수류탄, 동전들, 망치, 권총, 기관총) 도 똑같이 만들어준다.

Tag를 추가해준다. Item, Weapon

아이템 마다 Value를 원하는 대로 정해준다.

Assets 창에 Prefabs 파일을 하나 만들고 안에 하나씩 드래그로 넣어준다. 오리지널 프리펩 클릭

프리펩 Position의 X, Y, Z를 모두 0으로 초기화 시켜준다.
