### 플레이어 스크립트

```c#
public class Player : MonoBehaviour
{
    public float speed;
    float hAxis;
    float vAxis;
    bool wDown;
    bool jDown;
    
    bool isJump;
    bool isDodge;
    
    Vector3 moveVec;
    Vector3 dodgeVec;
    
    Rigidbody rigid;
    Animator anim;
    
    void Awake()
    {
        rigid = GetComponent<Rigidbody>();
        anim = GetComponentInChildren<Animator>();
    }
    
    void Start()
    {
        
    }
    
    void Update()
    {
        GetInput();
        Move();
        Turn();
        Jump();
        Dodge();
    }
    
    void GetInput()
    {
        hAxis = Input.GetAxisRaw("Horizontal");
        vAxis = Input.GetAxisRaw("Vertical");
        wDown = Input.GetButton("Walk");
        jDown = Input.GetButtonDown("Jump");
    }
    
    void Move()
    {
        moveVec = new Vector3(hAxis, 0, vAxis).normalized;
        
        if(isDodge)
            moveVec = dodgeVec; // 회피 중에는 움직임 벡터 -> 회피방향 벡터로 바뀌게함
       	transform.position += moveVec; * speed * (wDown ? 0.3f : 1f) *Time.deltaTime;
        
        anim.SetBool("isRun", moveVec != Vector3.zero);
        anim.SetBool("isWalk", wDown);
    }
    
    void Turn()
    {
        transform.LookAt(transform.position + moveVec);
    }
    
    void Jump()
    {
        if(jDown && moveVec == Vector3.zero && !isJump && !isDodge)
        {
            rigid.AddForce(Vector3.up * 15, ForceMode.Impulse);
            anim.SetBool("isJump", true);
            anim.SetTrigger("doJump");
            isJump = true;
        }
	}
    
    void Dodge()
    {
        if(jDown &&  moveVec != Vector3.zero && !isJump && !isJump)
            dodgeVec = moveVec;
        {
            speed * = 2;
            anim.SetTrigger("doDodge");
            isDodge = true;
            
            Invoke("DodgeOut", 0.5f);
        }
	}
    
    void DodgeOut()
    {
        speed *= 0.5f;
        isDodge = false;
	}
    
    void OnCollisionEnter(Collision collision)
    {
        if(collision.gameObject.tag == "Floor")
        {
            anim.SetBool("isJump", false);
            isJump = false;
        }
	}
}
```

바닥 오브젝트의 태그를 새로 추가하여 지정

Animator에서 Jump, Dodge

점프, 착지, 회피 애니메이션을 Transition으로 연결

점프와 착지를 연결

Any State -> Dodge

Has Exit Time 체크 해제

Transition Duration: 0



Dodge -> Exit

Transition Duration: 0.1



Jump -> Land

Has Exit Time 체크 해제

Transition Duration: 0

IsJump false



Land -> Exit

Transition Duration: 0.1



중력 설정

Edit > Project Settings > Physics

조절 (영상에서는 -25정도로 했음)

rigid.AddForce(Vector3.up * 15, ForceMode.Impulse);

여기서 곱해지는 숫자를 바꾸며 조절해준다.

WorkSpace를 Static으로 바꿔준다.

Static으로 해놓는 이유: Collision Dection > Continuous는 Static과 충돌할 때 효과적이기 떄문이다.

지형 오브젝트에 리지드 바디 추가하기 (Use Gravity를 끄고 Is Kinematic을 체크)

벽의 박스 콜라이더에서 Paste Component As New



마찰력이 없는 벽을 만들기 위해 Physic Material을 추가해준다.

Dynamic Friction, Static Friction을 0으로 설정한다.

Friction Combine : Minimum으로 설정해준다.



회피 애니메이션 속도를 보고 느리다면 애니메이터에 들어가서 speed를 조정해준다.

