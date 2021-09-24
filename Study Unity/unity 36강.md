따라다니는 보조 무기 만들기

Pixel Per Unit : 24

Filter Mode : Paint (no filter)

Compression : None

기존 프리펩을 Ctrl + D로 복사하여 보조 총알로 변경

Follower Bullet으로 바꾸어줌

총알 스프라이트 바꿔주기

박스 콜라이더 크기 맞춰주기



Follower 스크립트 만들기

### Follower 스크립트

```c#
public class Follower : MonoBehaviour
{
    public float maxShotDelay;
    public float curShotDelay;
    public ObjectManager objectManager;
    
    public Vector3 followPos;
    public int followDelay;
    public Transform parent;
    public Queue<Vector3> parentPos;
    
    private void Awake()
    {
        parentPos = new Queue<Vector3>();
    }
    
    void Update()
    {
        Watch();
        Follow();
        Fire();
        Reload();
	}
    
    void Watch()
    {
        // #. Input Pos
        if(!parentPos.Contains(parent.position)) // 부모 위치가 가만히 있으면 저장하지 않음
        	parentPos.Enqueue(parent.position);
        
        // #. Output Pos
        if(parentPos.Count > followDelay) // 큐에 일정 데이터 갯수가 채워지면 그 때부터 반환하도록 작성
        	followPos = parent.Pos.Dequeue();
        else if(parentPos.Count < followDelay) // 큐가 채워지기 전까진 부모 위치 적용
            followPos = parent.position;
    }
    
    void Follow()
    {
        transform.position = followPos;
        
    }
    
    void Fire()
    {
        if(!Input.GetButton("Fire1"))
            return;
        
        if(curShotDelay < maxShotDelay)
        	return;
       
        GameObject bullet = objectManager.MakeObj("BulletFollower");
                bullet.transform.position = transform.position;
                
        		Rigidbody2D rigid = bullet.GetComponent<Rigidbody2D>();
       			rigid.AddForce(Vector2.up*10, ForceMode2D, Impulse);
        
        curShotDelay = 0;
	}
    
     void Reload()
    {
        curShotDelay += Time.deltaTime;
        
	}
    
}
```

프리펩 넣기

부모 변수에 플레이어를 할당

Queue : 먼저 입력된 데이터가 먼저 나가는 자료구조 (FIFO)

Enqueue() : 큐에 데이터를 저장하는 함수

Dequeque() : 큐의 첫 데이터를 빼면서 반환하는 함수

Follower0의 부모 : 플레이어

Follower1의 부모 : Follower0 이런 식으로 만들기 



### 오브젝트 매니저 스크립트

```c#
public class ObjectManager : MonoBehaviour
{
    public GameObject enemyLPrefab; // 프리펩 변수 생성 후
    public GameObject enemyMPrefab; // 꼭 할당하기
    public GameObject enemySPrefab;
    public GameObject itemCoinPrefab;
    public GameObject itemPowerPrefab;
    public GameObject itemBoomPrefab;
    public GameObject bulletPlayerAPrefab;
    public GameObject bulletPlayerBPrefab;
    public GameObject bulletEnemyAPrefab;
	public GameObject bulletEnemyBPrefab;
    public GameObject bulletFollowerPrefab;
    
    GameObject[] enemyL;
    GameObject[] enemyM;
    GameObject[] enemyS;
    
    GameObject[] itemCoin;
    GameObject[] itemPower;
    GameObject[] itemBoom;
    
    GameObject[] bulletPlayerA;
    GameObject[] bulletPlayerB;
    GameObject[] bulletEnemyA;
    GameObject[] bulletEnemyB;
    GameObject[] bulletFollower;
    
    GameObject[] targetPool;
    
    void Awake()
    {
        enemyL = new GameObject[10]; // 한번에 등장할 개수를
        enemyM = new GameObject[10]; // 고려하여 배열 길이 할당
        enemyS = new GameObject[10];
        
        itemCoin = new GameObject[10];
        itemPower = new GameObject[10];
        itemBoom = new GameObject[10];
        
        bulletPlayerA = new GameObject[100];
        bulletPlayerB = new GameObject[100];
        bulletEnemyA = new GameObject[100];
        bulletEnemyB = new GameObject[100];
        bulletFollower = new GameOBject[100];
        
        Generate();
    }
    
    void Generate()
    {
        // #1. Enemy
        for(int index = 0; index < enemyL.Length; index++)
        {
            enemyL[index] = Instantiate(enemyLPrefab);
        	enemyL[index].SetActive(false); // Instantiate() 생성후 바로 비활성화
        }
            
        for(int index = 0; index < enemyM.Length; index++)
        {
            enemyM[index] = Instantiate(enemyMPrefab);
            enemyM[index].SetActive(false);
        }
        
        for(int index = 0; index < enemyS.Length; index++)
        {
            enemyS[index] = Instantiate(enemySPrefab);
            enemyS[index].SetActive(false);
        }
        
        // #2. Item
        for(int index = 0; index < itemCoin.Length; index++)
        {
            itemCoin[index] = Instantiate(itemCoinPrefab);
            itemCoin[index].SetActive(false);
        }
        
        for(int index = 0; index < itemPower.Length; index++)
        {
            itemPower[index] = Instantiate(itemPowerPrefab);
            itemPower[index].SetActive(false);
        }
        
        for(int index = 0; index < itemBoom.Length; index++)
        {
            itemBoom[index] = Instantiate(itemBoomPrefab);
            itemBoom[index].SetActive(false);
        }
        // #3. Bullet
        for(int index = 0; index < bulletPlayerA; index++)
        {
            bulletPlayerA[index] = Instantiate(bulletPlayerAPrefab);
            bulletPlayerA[index].SetActive(false);
        }
         
        for(int index = 0; index < bulletPlayerB; index++)
        {
            bulletPlayerB[index] = Instantiate(bulletPlayerBPrefab);
            bulletPlayerB[index].SetActive(false);
        }
        
        for(int index = 0; index < bulletEnemyA; index++)
        {
            bulletEnemyA[index] = Instantiate(bulletEnemyAPrefab);
            bulletEnemyA[index].SetActive(false);
        }
            
        for(int index = 0; index < bulletEnemyB; index++)
        {
            bulletEnemyB[index] = Instantiate(bulletEnemyBPrefab);
        bulletEnemyB[index].SetActive(false);
        }
        
        for(int index = 0; index < bulletFollower; index++)
        {
            bulletFollower[index] = Instantiate(bulletFollowerPrefab);
            bulletFollower[index].SetActive(false);
        }
    }
    
    public GameObject MakeObj(string type)
    {
        switch (type)
        {
            case "EnemyL":
                targetPool = enemyL;
            	break;
            case "EnemyM":
                targetPool = enemyM;
                break;
            case "EnemyS":
                targetPool = enemyS;
                break;
            case "ItemCoin":
                targetPool = itemCoin;
                break;
            case "ItemPower":
                targetPool = itemPower;
                break;
            case "ItemBoom":
                targetPool = itemBoom;
                break;
            case "BulletPlayerA":
                targetPool = bulletPlayerA;
                break;
            case "BulletPlayerB":
                targetPool = bulletPlayerB;
                break;
            case "BulletEnemyA":
                targetPool = bulletEnemyA;
                break;
            case "BulletEnemyB":
                targetPool = bulletEnemyB;
                break; 
            case "BulletFollower":
                targetPool = bulletFollower;
                break;
        }
        for(int index = 0; index < targetPool.Length; index++)
        {
        	if(!targetPool[index].activeSelf) // 비활성화된 오브젝트에 접근하여 활성화 후, 반환
            {
            	targetPool[index].SetActive(true);
                return targetPool[index];
            }
        }
        
        return null;
    }
    
    public GameObject[] GetPool(string type)
    {
        switch (type)
        {
            case "EnemyL":
                targetPool = enemyL;
            	break;
            case "EnemyM":
                targetPool = enemyM;
                break;
            case "EnemyS":
                targetPool = enemyS;
                break;
            case "ItemCoin":
                targetPool = itemCoin;
                break;
            case "ItemPower":
                targetPool = itemPower;
                break;
            case "ItemBoom":
                targetPool = itemBoom;
                break;
            case "BulletPlayerA":
                targetPool = bulletPlayerA;
                break;
            case "BulletPlayerB":
                targetPool = bulletPlayerB;
                break;
            case "BulletEnemyA":
                targetPool = bulletEnemyA;
                break;
            case "BulletEnemyB":
                targetPool = bulletEnemyB;
                break; 
            case "BulletFollower":
                targetPool = bulletFollower;
                break;
        }
        
        return targetPool;
    }
}
```



### 플레이어 스크립트

```c#
public class Player : MonoBehaviour
{
    public float speed;
    public bool isTouchTop;
    public bool isTouchBottom;
    public bool isTouchRight;
    public bool isTouchLeft;
    
    public int life;
    public int score;
    public float speed;
    public int maxPower;
    public int power;
    public int maxBoom;
    public int boom;
    
    public float maxShotDelay; // 최대
    public float curShotDelay; // 현재
    
    public GameObject bulletObjA; // 총알 프리렙 저장
    public GameObject bulletObjB;
    public GameObject boomEffect;
    
    public GamegameManager gameManager;
    public ObjectgameManager objectManager;
    public bool isHit;
    public bool isBoomTime;
    
    public GameObject[] followers;
    
    Animator anim;
    
    void Awake()
    {
        anim = GetCompoent<Animator>();
	}
    
    void Update()
    {
        Move();
        Fire();
        Boom();
        Reload();
	}
    
    void Move()
    {
        float h = Input.GetAxisRaw("Horizontal");
        if((isTouchRight && h == 1) || (isTouchLeft && h == -1))
            h = 0;
        
        float v = Input.GetAxisRaw("Vertical");
        if((isTouchTop &&v == 1) || (isTouchBottom && v ==  - 1))
            v = 0;
        
        Vector3 curPos = transform.position;
        Vector3 nextPos = new Vector3(h, v, 0) * speed * Time.deltaTime; // transform 이동에는 Time.DeltaTime를 이용해야함.
        
        transform.position = curPos + nextPos;
        
        if(Input.GetButtonDown("Horizontal") || 
           Input.GetButtonUp("Horizontal"))
        {
            anim.SetInteger("Input", (int)h);
		}
    }
    
    void Fire()
    {
        if(!Input.GetButton("Fire1"))
            return;
        
        if(curShotDelay < maxShotDelay)
        	return;
       
        switch (power)
        {
        	case 1; // 한발 발사 될 때
        		GameObject bullet = objectManager.MakeObj("BulletPlayerA");
                bullet.transform.position = transform.position;
                
        		Rigidbody2D rigid = bullet.GetComponent<Rigidbody2D>();
       			rigid.AddForce(Vector2.up*10, ForceMode2D, Impulse);
                break;
        	case 2; // 두발 발사 될 때
                GameObject bulletR = objectManager.MakeObj("BulletPlayerA");
                bulletR.transform.position = transform.position + Vector3.right * 0.1f;
                
                GameObject bulletL = objectManager.MakeObj("BulletPlayerA");
                bulletL.transform.position = transform.position +Vector3.left * 0.1f;
                
       			Rigidbody2D rigid = bulletR.GetComponent<Rigidbody2D>();
                Rigidbody2D rigid = bulletL.GetComponent<Rigidbody2D>();
      			rigidR.AddForce(Vector2.up*10, ForceMode2D, Impulse);
                rigidL.AddForce(Vector2.up*10, ForceMode2D, Impulse);
                break;
            default:
        		GameObject bulletRR = objectManager.MakeObj("BulletPlayerA");
                bulletRR.transform.position = transform.position + Vector3.right * 0.35f; // 오른쪽
                
                GameObject bulletCC = objectManager.MakeObj("BulletPlayerB");
                bulletRR.transform.position = trnasform.position + Vector3.right * 0.35f; // 중앙
                
                GameObject bulletLL = objectManager.MakeObj("BulletPlayerA");
                bulletLL.transform.position = transform.position + Vector3.left * 0.35f, // 왼쪽
                
       			Rigidbody2D rigidRR = bullet.GetComponent<Rigidbody2D>();
                Rigidbody2D rigidCC = bullet.GetComponent<Rigidbody2D>();
                Rigidbody2D rigidLL = bullet.GetComponent<Rigidbody2D>();                                 
      			rigid.AddForce(Vector2.up*10, ForceMode2D, Impulse);
                rigid.AddForce(Vector2.up*10, ForceMode2D, Impulse);
				rigid.AddForce(Vector2.up*10, ForceMode2D, Impulse);
                break;
		}
        
        curShotDelay = 0;
	}
    
        void Reload()
    {
        curShotDelay += Time.deltaTime;
        
	}
    
    void Boom()
    {
       if(!Input.GetButton("Fire2"))
           return;
        
       if(isBoomTime)
       {
           return;
           
           if(boom == 0)
               return;
           
         boom--;  
         isBoomTime = true;
         gameManager.UpdateBoomIcon(boom);
           
         // #1. Effect visible
         boomEffect.SetActive(true)
         Invoke("OffBoomEffect", 4f);
                    
         // #2. Remove Enemy
         GameObject[] enemiesL = objectManager.GetPool("EnemyL");
         GameObject[] enemiesM = objectManager.GetPool("EnemyM");
         GameObject[] enemiesS = objectManager.GetPool("EnemyS");
           
         for(int index = 0; index < enemiesL.Length; index++)
         {
             if(enemiesL[index].activeSelf)
             {
             	Enemy enemyLogic = enemiesL[index].GetComponent<Enemy>();
            	enemyLogic.OnHit(1000);   
             }
         }
           for(int index = 0; index < enemiesM.Length; index++)
         {
             if(enemiesM[index].activeSelf)
             {
             	Enemy enemyLogic = enemiesM[index].GetComponent<Enemy>();
            	enemyLogic.OnHit(1000);   
             }
         }
           for(int index = 0; index < enemiesS.Length; index++)
         {
             if(enemiesS[index].activeSelf)
             {
             	Enemy enemyLogic = enemiesS[index].GetComponent<Enemy>();
            	enemyLogic.OnHit(1000);   
             }
         }
                    
         // #3. Remove Enemy Bullet
         GameObject[] bulletsA = objectManager.GetPool("EnemyBulletA");
		 GameObject[] bulletsB = objectManager.GetPool("EnemyBulletB");
           
         for(int index = 0; index < bulletsA.Length; index++)
         {
            if(bulletsA[index].activeSelf)
            {
               bulletsA[index].SetActive(false);
            }
         }
         for(int index = 0; index < bulletsB.Length; index++)
         {
            if(bulletsB[index].activeSelf)
            {
               bulletsB[index].SetActive(false);
            }
         }
    }
    
    void OnTriggerEnter2D(Collider2D collision)
    {
        if(collision.gameObject.tag == "Border")
        {
            switch(collision.gameObject.name)
            {
                case "Top":
                    isTouchTop = true;
                    break;
                case "Bottom":
                    isTouchBottom = true;
                    break;
                case"Right":
                    isTouchRight = true;
                    break;
                case"Left":
                    isTouchLeft = true;
                    break;
			}
        }
        else if(collision.gameObject.tag == "Enemy" || collision.gameObject.tag == "EnemyBullet")
        {
            if(isHit) // 총알 두개를 동시에 맞았을 때 한번에 목숨 2개가 깎이는 것을 방지함
                return;
            life--;
            gameManager.UpdateLifeIcon(life);
            
            if(life == 0)
            {
                gameManager.GameOver();
            }
            else
            {
                gameManager.RespawnPlayer();
            }
            gameObject.SetActive(false);
            Destroy(collision.gameObject);
        }
        else if(collision.gameObject.tag == "Item")
        {
            Item item = collision.gameObject.GetComponent<Item>();
            switch (item.type)
            {
                case "Coin":
                    score += 1000;
                    break;
                case "Power":
                    if(power == maxPower)
                        score += 500;
                    else
                    {
                        power++;
                        AddFollower();
                    }
                    break;
                case "Boom":
                    if(boom == maxBoom)
                        score += 500;
                    else
                    {
                        boom++;
                        gameManager.UpdateBoomIcon(boom);
                    }   
                    break;
            }
            Destroy(collision.gameObject);
        }
    }
    
    void OffBoomEffect()
    {
        boomEffect.SetActive(false);
        isBoomTIme = false;
    }
    
    void AddFollower()
    {
        if(power == 4)
            followers[0].SetActive(true);
        else if(power == 5)
            followers[1].SetActive(true);
         else if(power == 6)
            followers[2].SetActive(true);
    }
        
    void OnTriggerExit2D(Collider2D collision)
    {
        if(collision.gameObject.tag == "Border")
        {
            switch(collision.gameObject.name)
            {
                case "Top":
                    isTouchTop = false;
                    break;
                case "Bottom":
                    isTouchBottom = false;
                    break;
                case"Right":
                    isTouchRight = false;
                    break;
                case"Left":
                    isTouchLeft = false;
                    break;
			}
        }
    }
}
```

최대 파워가 올라간 대로 수치와 로직을 수정해야함 