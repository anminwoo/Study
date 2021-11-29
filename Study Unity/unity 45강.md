### 자동 회전 방지

플레이어

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
    public Camera followCamera;
    
    public int maxAmmo;
    public int maxCoin;
    public int maxHealth;
    public int maxHasGrenades;
        
    float hAxis;
    float vAxis;
    
    bool wDown;
    bool jDown;
    bool fDown;
    bool rDown;
    bool iDown;
    bool sDown1;
    bool sDown2;
    bool sDown3;
    
    bool isJump;
    bool isDodge;
    bool isSwap;
    bool isReload;
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
        Reload();
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
        rDown = Input.GetButtonDown("Reload");
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
        
        if(isSwap || isReload || !isFireReady)
            moveVec = Vector3.zero;
        
       	transform.position += moveVec; * speed * (wDown ? 0.3f : 1f) *Time.deltaTime;
        
        anim.SetBool("isRun", moveVec != Vector3.zero);
        anim.SetBool("isWalk", wDown);
    }
    
    void Turn()
    {
        // 1. 키보드에 의한 회전
        transform.LookAt(transform.position + moveVec);
        
        // 2. 마우스에 의한 회전
        if(fDown)
        {
            Ray ray = followCamera.ScreenPointToRay(Input.mousePosition);
      	    RaycastHit rayHit;
        	if(Physics.Raycast(ray, out rayHit, 100))
        	{
            	Vector3 nextVec = rayHit.point - transform.position;
                nextVec.y = 0;
           		transform.LookAt(transform.position + nextVec);
        	}
        }
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
    
    void  Reload()
    {
        if(equipWeapon == null) // 장착한 무기가 없다면 return;
            retrun;
        
        if(equipWeapon.type == Weapon.Type.Melee) // 장착한 무기가 근접 무기 라면 return;
            return;
        if(ammo == 0)
            return;
        
        if(rDown && !isJump && !isDodge && !isSwap && !isFireReady)
        {
            anim.SetTrigger("doReload");
            isReload = true;
            
            Invoke("ReloadOut", 3f); // 장전 시간은 자신이 원하는대로
        }
    }
    
    void ReloadOut()
    {
        int reAmmo = ammo < equipWeapon.maxAmmo ? ammo : equipWeapon.maxAmmo; // 가지고 있는 총알이 장착한 무기의 탄창 수 보다 적다면 가지고 있는 총알 만큼만 채워줌
        equipWeapon.curAmmo = reAmmo;
        ammo -= reAmmo;
        isReload = false;
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
    
    void FreezeRotation() // 자동회전방지
    {
        rigid.angularVelocity = Vector3.zero;
        
    }
    
    void FixedUpdate()
    {
        FreezeRotation();
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

AngularVelocity: 물리 회전 속도



### 충돌 레이어 설정

Tag & Layers에서 Floor, Player, PlayerBullet, BulletCase 태그 추가

각각 알맞게 태크 붙여주기

Layer Collision Masks: 레이어들의 상호간 충돌 설정

여기서 충돌 설정을 해줌.

는 와 충돌 설정

PlayerBullet

| 대상         | 충돌설정          |
| ------------ | ----------------- |
| BulletCase   | Floor, BulletCase |
| PlayerBullet | Player만 제외     |



벽 관통 방지 부터 보기