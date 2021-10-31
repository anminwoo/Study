### 플레이어 스크립트

```c#
public class Player : MonoBehaviour
{
    public float speed;
    public GameObject[] weapons;
    public bool[] hasWeapons;
    
    float hAxis;
    float vAxis;
    bool wDown;
    bool jDown;
    bool iDown;
    
    bool isJump;
    bool isDodge;
    
    GameObject nearObject;
    
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
        Interation();
    }
    
    void GetInput()
    {
        hAxis = Input.GetAxisRaw("Horizontal");
        vAxis = Input.GetAxisRaw("Vertical");
        wDown = Input.GetButton("Walk");
        jDown = Input.GetButtonDown("Jump");
        iDown = Input.GetButtonDown("Interation");
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
    
    void Interation()
    {
        if(iDown && nearObject != null && !isJump && !isDodge)
        {
            if(nearObject.tag == "Weapon")
            {
                Item item = nearObject.GetComponent<Item>();
                int weaponIndex = item.value
                hasWeapons[weaponIndex] = true;
                
                Destroy(nearObject);
            }
        }
    }
    
    void OnCollisionEnter(Collision collision)
    {
        if(collision.gameObject.tag == "Floor")
        {
            anim.SetBool("isJump", false);
            isJump = false;
        }
	}
    
    void OnTriggerStay(Collider other)
    {
        if(other.tag == "Weapon")
            nearObject = other.gameObject;
    }
    
    void OnTriggerExit(Collider other)
    {
        if(other.tag == "Weapon")
        	nearObject = null;
    }
}
```

프로젝트 세팅 -> Input Manager에서 상호작용 버튼을 추가해준다.

Size : 20

Name : Interation

Positive Button : e 로 설정해준다.

Player (Script) 에서 Has Weapons -> Size를 3으로 해준다.

무기 장착 부터 듣기

Player ->  Mash Object -> Bone_Body -> Bone_Shoulder_R -> RightHand

RIghtHand에서 3D Object -> Cylinder 생성 (Weapon Point)

위치를 손 안에 들어가도록 해줌 Scale X, Y, Z 4로 해줌

캡슐 콜라이더는 없애기. Mash Randerer 체크 해제

무기 프리펩를 무기 위치에 생성

Weapon Point를 자연스럽게 옮겨줌.

배치한 무기 오브젝트를 모두 비활성화 & 배열에 할당

Player Inspector 창에 자물쇠를 잠금으로 하고 Weapon Point에 넣었던 무기들을 모두 선택한 후 Weapons -> Size에 끌어다놓는다. 그러면 자동으로 넣어진다.

무기 교체 부터 듣기