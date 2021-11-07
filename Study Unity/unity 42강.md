### 플레이어 스크립트

```c#
public class Player : MonoBehaviour
{
    public float speed;
    public GameObject[] weapons;
    public bool[] hasWeapons;
    public GameObject[] grenades;
    public int hasGrenades;
    
    public int ammo;
    public int coin;
    public int health;
    public int hasGrenades;
    
    public int maxAmmo;
    public int maxCoin;
    public int maxHealth;
    public int maxHasGrenades;
        
    float hAxis;
    float vAxis;
    
    bool wDown;
    bool jDown;
    bool iDown;
    bool sDown1;
    bool sDown2;
    bool sDown3;
    
    bool isJump;
    bool isDodge;
    bool isSwap;
    
    Vector3 moveVec;
    Vector3 dodgeVec;
    
    Rigidbody rigid;
    Animator anim;
    
    GameObject nearObject;
    GameObject equipWeapon;
    int equipWeaponIndex = -1;
    
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
        Swap();
        Interation();
    }
    
    void GetInput()
    {
        hAxis = Input.GetAxisRaw("Horizontal");
        vAxis = Input.GetAxisRaw("Vertical");
        wDown = Input.GetButton("Walk");
        jDown = Input.GetButtonDown("Jump");
        iDown = Input.GetButtonDown("Interation");
        sDown1 = Input.GetButtonDown("Swap1");
        sDown2 = Input.GetButtonDown("Swap2");
        sDown3 = Input.GetButtonDown("Swap3");
    }
    
    void Move()
    {
        moveVec = new Vector3(hAxis, 0, vAxis).normalized;
        
        if(isDodge)
            moveVec = dodgeVec; // 회피 중에는 움직임 벡터 -> 회피방향 벡터로 바뀌게함
        
        if(isSwap)
            moveVec = Vector3.zero;
        
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
        if(jDown && moveVec == Vector3.zero && !isJump && !isDodge && !isSwap)
        {
            rigid.AddForce(Vector3.up * 15, ForceMode.Impulse);
            anim.SetBool("isJump", true);
            anim.SetTrigger("doJump");
            isJump = true;
        }
	}
    
    void Dodge()
    {
        if(jDown &&  moveVec != Vector3.zero && !isJump && !isDodge && ! isSwap)
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
    
    void Swap()
    {
        if(sDown1 && (!hasWeapons[0] || equipWeaponIndex == 0))
            return;
        
        if(sDown2 && (!hasWeapons[0] || equipWeaponIndex == 0))
            return;
        
        if(sDown3 && (!hasWeapons[0] || equipWeaponIndex == 0))
            return;
        
        int weaponIndex = -1;
        if(sDown1) weaponIndex = 0;
        if(sDown2) weaponIndex = 1;
        if(sDown3) weaponIndex = 2;
            
        if((sDown1 || sDown2 || sDown3) && !isJump && !isDodge)
        {
            if(equipWeapon != null)
            	equipWeapon.SetActive(false);
            
            equipWeaponIndex = weaponIndex;
            equipWeapon = weapons[weaponIndex];
            equipWeapon.SetActive(true);
            
            anim.SetTrigger("doSwap");
            
            isSwap = true;
            
            Invoke("SwapOut", 0.4f);
        }
    }
    
    void SwapOut()
    {
        isSwap = false;
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
    
    void OnTriggerEnter(Collider other)
    {
        if(other.tag == "Item")
        {
            Item item = other.GetComponent<Item>();
            switch(item.type)
            {
                case Item.Type.Ammo:
                    ammo += item.value;
                    if(ammo > maxAmmo)
                        ammo = maxAmmo;
                    break;
                case Item.Type.Coin:
                    Coin += item.value;
                    if(coin > maxCoin)
                        coin = maxCoin;
                    break;
                case Item.Type.Heart:
                    Heart += item.value;
                    if(health > maxHealth)
                        health = maxHealth;
                    break;
                case Item.Type.Grenade:
                    grenades[hasGrenades].SetActive(true);
                    hasGrenades += item.value;
                    if(hasGrenades > maxHasGrenades)
                        hasGrenades = maxHasGrenades;
                    break;
            }
            Destroy(ohter.gameObject);
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

인스펙터 창에서 초기값을 설정해준다.

### 공전 물체 만들기

Create Empty (Name: Grenade Group)

그리고 Grenade Group 안에 Create Empty * 4 만들어준다.

각각 Front, Back, Right, Left

| 방향  | X    | Y    | Z    |
| ----- | :--- | ---- | ---- |
| Front | 0    | 0    | 2    |
| Back  | 0    | 0    | -2   |
| Right | 2    | 0    | 0    |
| Left  | -2   | 0    | 0    |

4개를 모두선택한 후 아이콘을 넣어 잘 볼 수있도록 함

4방향으로 공전 오브젝트에 수류탄 프리펩을 넣어준다. (Rotation Z: 30)

#### 재질 바꾸기

Assets > QuadAction Pack > Materials에서 **Weapon Grenade Orbit**을 Weapon Grenade -> Mesh Object에 넣어준다.

#### 파티클 효과 추가

| Light 추가           | Range: 2          | Color: 보라색 계열       |
| -------------------- | ----------------- | ------------------------ |
| Particle System 추가 | Renderer          | Material -> Default-Line |
| Shape                | Sphere Radius : 1 | Radius Thichkness: 1     |
| Emission ->          | Rate over Time: 0 | Rate of Distance: 10     |
| Simulation Space     | World             |                          |
| 파티클 효과          | Color             | 보라색 계열 그라데이션   |

Rate of Distance : 파티클의 위치 변화에 따라 입자 생성



2Scripts에 Orbit 스크립트 만들기

4방향(Front, Back, Right, Left)에 스크립트 추가

### 공전 구현

```c#
public class Orbit : MonoBehaviour
{
    public Transform target;
    public float orbitSpeed;
    Vector3 offset;
    
    void Start()
    {
        offSet = transform.position - target.position;
    }
    
    void Update()
    {
        transform.position = target.position + offSet;
        trasform.RotateAround(target.position,
                             Vector3.up,
                             orbitSpeed * Time.deltaTime);
        offSet = transform.position - target.position;
    }
}
```

RotateAround() : 타겟 주위를 회전하는 함수

Target : Player

Orbit Speed : 15

RotateAround()는 목표가 움직이면 일그러지는 단점이 있다.

그래서 offSet를 사용해서 계속 플레이어와 함께 하도록 해준다.

파티클이 플레이어를 따라다니는 파티클 효과가 나오는 건 Particle System -> Simulation Space : World, Emission : Rate over Distance일 때 나온다.



Player Inspector를 잠금하고 각 수류탄들을 모두 선택한 후 플레이어 수류탄 배열 변수에 할당시켜준다.

각 수류탄을 인스펙터 창에서 비활성화 시켜준다.

