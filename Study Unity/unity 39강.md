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
    
    Vector3 moveVec;
    
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
        if(jDown && !isJump)
        {
            rigid.AddForce(Vector3.up * 15, ForceMode.Impulse);
            anim.SetBool("isJump", true);
            anim.SetTrigger("doJump");
            isJump = true;
        }
	}
    
    OnCollisionEnter(Collision collision)
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