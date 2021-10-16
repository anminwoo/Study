에셋 다운로드 하기

에셋 패키지를 열고 Import 눌러서 가져오기

### 지형 만들기

3D Objects에서 Cube 만들기

Rendering - Lighting Settings - Generate LIghting으로 칙칙함을 해결한다.

Scale을 X: 100, Y: 1, Z: 100 이름을 Floor로 하기

큐브를 하나 더 만들고 Wall로 하기  X: 110, Y: 10, Z: 10 Position은 Z: - 55

오브젝트 복사 : Ctrl + D

모든 모서리를 벽으로 감싸준다.

안쪽 벽은 Mesh Renderer를 비활성화하여 겉보기만 감추기

Assets 안에 Materials 파일 만들어주기 Material 생성 Floor라고 이름 짓기(바닥 색깔) Albedo 옆 원을 눌러 텍스쳐 이미지를 선택한다. 이미지를 씌워준다.

Tiling 숫자를 늘려서 텍스쳐를 타일형태로 배치해준다.(X: 10, Y: 10)

원하는 색깔로 바꿔준다.

프리펩(Prefab) : 게임 오브젝트를 에셋으로 보관된 형태

필요한 컴포넌트 :  Collider, Script, Rigidbody

Capsule Collider를 플레이어에 맞게 위치, 크기 조절



### 플레이어 스크립트

```c#
public class Player : MonoBehaviour
{
    public float speed;
    float hAxis;
    float vAxis;
    
    Vector3 moveVec;
    
    void Start()
    {
        
    }
    
    void Update()
    {
        hAxis = Input.GetAxisRaw("Horizontal");
        vAxis = Input.GetAxisRaw("Vertical");
        
        moveVec = new Vector3(hAxis, 0, vAxis).normalized;
        
        transform.position += moveVec; * speed * Time.deltaTime;
    }
}
```

GetAxisRaw() : Axis 값을 정수로 반환하는 함수

normalized : 방향 값이 1로 보정된 벡터



Freeze Rotation에서 X, Z 고정

transform 이동은 물리 충돌을 무시하는 경우가 발생함

Collision Detection을 Continuous로 변경해준다.



애니메이션 부터 보기

플레이어를 누르고  Mash Object에 Animator를 플레이어 자식 오브젝트에 투입시킨다.

애니메이터에 들어가서 Entry -> Idle -> Walk -> Run -> Idle 연결해주기

애니메이션 상태 사이를 Transition으로 연결해준다. 

bool 타입 파라메터(매개변수) 추가

파라메터를 Condition에 사용해서 Transition 완성하기

Transition Duration를 조절한다. (Idle과 Run 사이를 1로해준다.)

Idle <- Run에서는 IsRun을 false로 해준다.

24분 이후 부터

