Create Empty (이름 : Bullet HandGun)

총알 효과로 Trail Renderer 추가

Material에 Element 0을 Default-Line으로 설정, Time : 0, Min Vertex Distance : 0

Trail Renderer Width를 세로는 0.5, 가로는 0.7에 key 생성 후 곡선 형태가 되도록 당겨준다.

Color : (169, 222, 20) 부터 (255, 194, 0) 연두색 부분 화살표 Location 10.3%

끝부분 알파값을 50으로 해준다.

리지드 바디, 콜라이더(Sphere) 추가 위치 0, 0, 0으로 맞춰주기

Radius : 0.3



ctrl + d로 복사 후 Bullet SubMachineGun 

Trail Renderer에서 Time : 0.1

기관총 색과 어울리는 색으로 해준다. location : 10% 알파값 : 50

Width도 0.3정도 부터 시작하도록 설정

탄피 모델을 꺼내서 하이라키창에 넣어주기

탄피안의 Mesh Object의 Scale를 0.5로 맞춰주기

Rigidbody,  Box Collider 추가

Box Collider X: 0.45, Y: 0.3, Z: 0.3

Bullet 스크립트 만들기

스크립트 생성

### Bullet 스크립트

```c#
public class Bullet : MonoBehaviour
{
    public int damage;
    
    void OnCollisionEnter(Collision collision)
    {
        if(collision.gameObject.tag == "Floor")
        {
            Destroy(gmaeObject, 3);
        }
        else if(collision.gameObject.tag == "Wall")
        {
            Destroy(gameObject);
        }
    }
}
```

Bullet HandGun, Bullet SubMachineGun, Bullet Case에 스크립트를 넣어준다.

데미지를 설정해준다. (권총  20, 머신건 7)

총과 탄피를 프리펩으로 저장

위치를 0, 0, 0으로 초기화해줌

QuadAcion -> Models-> Player에 있는 Shot 애니메이션을 애니메이터에 추가

Any State -> Shot -> Exit 연결, New Trigger 추가(doShot), Any State -> Shot 부분의 Conditions에 doShot넣어주기 Transition Duration: 0, Transition Offset: 0  

Shot -> Exit 부분 Transition Duration: 0.1, Transition Offset: 0



#### 플레이어 스크립트

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
    bool fDown;
    bool iDown;
    bool sDown1;
    bool sDown2;
    bool sDown3;
    
    bool isJump;
    bool isDodge;
    bool isSwap;
    bool isFireReady = true;
    
    Vector3 moveVec
    Vector3 dodgeVec;
    
    Rigidbody rigid;
    Animator anim;
    
    GameObject nearObject;
    Weapon equipWeapon;
    int equipWeaponIndex = -1;
    float fireDelay;
    
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
        Attack();
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
        fDown = Input.GetButton("Fire1");
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
        
        if(isSwap || !isFireReady)
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
    
    void Attack()
    {
        if(equipWeapon == null)
            return;
        
        fireDelay += Time.deltaTime;
        isFireReady = equipWeapon.rate < fireDelay;
        
        if(fDown && isFireReady && !isDodge && !isSwap)
        {
            equipWeapon.Use();
        	anim.SetTrigger(equipWeapon.type == Weapon.Type.Melee ? "doSwing" : "doShot");
        	fireDelay = 0;
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
            	equipWeapon.gameObject.SetActive(false);
            
            equipWeaponIndex = weaponIndex;
            equipWeapon = weapons[weaponIndex].GetComponent<Weapon>();
            equipWeapon.gameObject.SetActive(true);
            
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

권총과 기관총의 타입을 Range로 변경 권총: Rate(발사속도) 0.7, 서브 머신건 0.15



#### 무기 스크립트

```c#
public class Weapon : MnonBehaviour
{
    public enum Type { Melee, Range };
    public Type type;
    public int damage;
    public float rate;
    public BoxCollider meleeArea;
    public TrailRenderer trailEffect;
    public Transform bulletPos;
    public Transform bullet; // 프리펩 생성 위치
    public GameObject bullet; // 프리펩 저장 위치
    public Transform bulletCasePos;
    public GameObject bulletCase;
    
    public void Use()
    {
        if(type == Type.Melee)
        {
            StopCoroutine("Swing");
            StartCoroutine("Swing");
        }
        else if(type == Type.Melee)
        {
            StopCoroutine("Shot");
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
    
    IEnumerator Shot()
    {
        // 1.총알 발사
        GameObject intantBullet = Instantiate(bullet, bulletPos.position, bulletPos.rotation);
        Rigidbody bulletRigid = intantBullet.GetComponent<Rigidbody>();
        bulletRigid.velocity = bulletPos.foward * 50;
        
        yield return null;
        // 2. 탄피 배출
        GameObject intantCase = Instantiate(bulletCase, bulletCasePos.position, bulletCasePos.rotation);
        Rigidbody caseRigid = intantCase.GetComponent<Rigidbody>();
        Vector3 caseVec = bulletCasePos.forward *Random.Range(-3, -2) + Vector3.up(2, 3);
        caseRigid.AddForce(caseVec, ForceMode.Impulse);
        caseRigid.AddTorque(Vector3.up * 10, ForceMode.Impulse); // 탄피가 회전하며 떨어짐
    }
}
```

플레이어 안에 Create Empty(Bullet Pos) Poairion X: 1, Y: 2.5, Z: 1.8

Weapon HandGun안에 Create Empty(Case Pos) (머신건도 동일)

Global을 Local로 변경

적당히 탄피배출구쪽에 위치를 맞춰줌

발사 위치, 총알, 탄피 위치, 탄피를 변수에 넣어준다.

Add Tag에서 Wall을 만들어준다.벽에 태크 달아주기

재장전 구현하기 부터 듣기