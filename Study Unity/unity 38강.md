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
    bool wDown;
    
    Vector3 moveVec;
    
    Animator anim;
    
    void Awake()
    {
        anim = GetComponentInChildren<Animator>();
    }
    
    void Start()
    {
        
    }
    
    void Update()
    {
        hAxis = Input.GetAxisRaw("Horizontal");
        vAxis = Input.GetAxisRaw("Vertical");
        wDown = Input.GetButton("Walk");
        
        moveVec = new Vector3(hAxis, 0, vAxis).normalized;
       	transform.position += moveVec; * speed * (wDown ? 0.3f : 1f) *Time.deltaTime;
        
        anim.SetBool("isRun", moveVec != Vector3.zero);
        anim.SetBool("isWalk", wDown);
        
        
        transform.LookAt(transform.position + moveVec);
    }
}
```

GetAxisRaw() : Axis 값을 정수로 반환하는 함수

normalized : 방향 값이 1로 보정된 벡터

LookAt() : 지정된 벡터를 향해서 회전시켜주는 함수



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

Run -> Walk 에서 Has Exit Time 체크해제 ,Transition Duration 0.1

Walk -> Run 에서 IsWalk : false 나머지는 위와 동일

Walk -> Idle IsRun : false 나머지는 동일

Idle -> Walk IsWalk, IsRun 둘다  true 나머지는 동일



프로젝트 매니저로 들어가 Input Manager에서 새로운 Input을 위해 Size 늘리기 18 -> 19

Name : Walk

Positive Button : left shift

Alt Positive Button은 지우기



쿼터뷰 느낌이 나도록 카메라 위치, 회전 변경

| Position | X: 0  | Y: 21 | Z: -11 |
| -------- | ----- | ----- | ------ |
| Rotation | X: 60 | Y: 0  | Z: 0   |
| Scale    | X: 1  | Y: 1  | Z: 1   |



### 카메라 스크립트

follower 스크립트 만들기

메인카메라에 넣기

```c#
public class Follow : MonoBehaviour
{
    public Transform target;
    public Vector3 offset;
    
    void Update()
    {
        transform.position = target.position + offset;
    }
}
```

target에다가 플레이어 끌어다가 넣기

Offset에다가**Position** 적어주기

빈오브젝트 만들고 WorldSpace 만들고 지형 오브젝트를 모두 넣은 다음 45도 회전시킨다.

애니메이션 속도를 알맞게 변경해준다.

카메라가 보고있는 방향쪽 벽이 보이고 아닌쪽은 Mash Renderer로 안보이게 설정해준다.

카메라 오프셋 값을 조절하여 원하는 뷰를 찾는다.
