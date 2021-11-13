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
        fDown = Input.GetButtonDown("Interation");
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
        	anim.SetTrigger("doSwing");
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

애니메이터에서 트리거 만들고 (doSwing) Swing 모션 추가 후 Any State -> Swing -> Exit

Any State

Transition Duration : 0

Conditions에 doSwing 추가



Exit

Transition Duration : 0.1



공격 속도는 자신이 원하는데로 조정해줌

이펙트가 너무 사라지는 것 같다. -> Effect에서 TIme을 줄여줌