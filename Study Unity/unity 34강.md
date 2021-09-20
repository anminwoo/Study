오브젝트 풀링을 써야 하는 이유

생성과 삭제를 반복하면서 조각나 메모리가 쌓임

가비지컬렉트가 작동하며 렉이 심하게 걸림

가비지컬렉트(GC) : 쌓인 조각난 메모리를 비우는 기술



오브젝트 풀링 : 미리 생성해둔 풀에서 활성화/비활성화로 사용하는 것

Create Empty -> 이름 ObjectManager -> 스크립트 만들기



## 오브젝트 매니저 스크립트

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
    }
    
    public GameObject MakeObj(string type)
    {
        
        
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
        }
        return targetPool;
    }
}
```

게임에서 첫 로딩 시간 = 장면 배치 + 오브젝트 풀 생성



## 게임 매니저 스크립트

```c#
using UnityEngine.UI // UI를 사용하기 위해
using UnityEngine.SceneManagement // 재시작하기 위해
public class GameManager : MonoBehaviour
{
    public string[] enemyObjs;
    public Transform[] spawnPoints;
    
    public float maxSpawnDelay;
    public float curSpawnDelay;
    
    public GameObject player;
    public Text scoreText;
    public Image[] lifeImage;
    public Image[] boomImage;
    public GameObject gameOverSet;
    public ObjectManager objectManager;
    
    void Awake()
    {
        enemyObjs = new string[] { "EnemyS", "EnemyM", "EnemyL" };
    }
    
    void Update()
    {
        curSpawnDelay == Time.deltaTime;
        
        if(curSpawnDelay > maxSpawnDelay)
        {
            SpawnDelay();
            maxSpawnDelay = Random.Range(0.5f, 3f);
            SpawnDelay = 0; // 적 생성 후 딜레이 변수 0으로 초기화
        }
        
        //#.UI Score Update
        Player playerLogic = player.GetComponent<Player>();
        scoreText.text = string.Format("{0:n0}", playerLogic.score)playerLogic.score
    }
    
    void SpawnEnemy()
    {
        int ranEnemy = Random.Range(0, 3);
        int ranPoint = Random.Range(0, 5); // 이부분은 자신이 배치한 위치로 바꿔줘야함
       GameObject enemy = objectManager.MakeObj(enemyObjs[ranEnemy]);
        enemy.transform.position = spawnPoints[ranPoint].position;
        
        Rigidbody2D rigid = enemy.GetComponent<Rigidbody2D>();
        Enemy enemyLogic = enemy.GetComponent<Enemy>();
        enemyLogic.player = player; // 적 생성 직후 플레이어 변수를 넘겨줌
        enemyLogic.objectManager = objectManager;
        if(ranPoint == 5 || ranPoint == 6) // Right Spawn
        {
            enemy.transform.Rotate(Vector3.back * 90); // z축 단일
            rigid.velocity = new Vector2(enemyLogic.speed*(-1), -1);
		}
        else if(ranPoint == 7 || ranPoint == 8) // Left Spawn
        {
            enemy.transform.Rotate(Vector3.forward * 90);
            rigid.velocity = new Vector2(enemyLogic.speed, -1);
        }
        else // Front Spawn
        {
            rigid.velocity = new Vector2(0, enemyLogic.speed * (-1));
        }
    }
    
    public void UpdateLifeIcon(int life)
    {
        // #.UI Life Init Disable
        for(int index = 0; index < life; index++)
        {
            lifeImage[index].color = new Color(1, 1, 1, 0);
		}
        
        // #.UI Life Active
        for(int index = 0; index < life; index++)
        {
            lifeImage[index].color = new Color(1, 1, 1, 1);
        }
	}
    
    public void UpdateBoomIcon(int boom)
    {
        // #.UI Boom Init Disable
        for(int index = 0; index < 3; index++)
        {
            lifeImage[index].color = new Color(1, 1, 1, 0);
		}
        
        // #.UI Boom Active
        for(int index = 0; index < boom; index++)
        {
            lifeImage[index].color = new Color(1, 1, 1, 1);
        }
	}
    
    public void RespawnPlayer()
    {
        Invoke("RespawnPlayerExe", 2f);
    }
    
	void RespawnPlayerExe()
    {
        Invoke()
        player.transform.position = Vector3.down * 3.5f;
        player.SetActive(true);
        
        Player playerLogic = player.GetComponent<Player>();
        playerLogic.isHit = false;
    }
    
    public void GameOver()
    {
        gameOverSet.SetActive(true);
    }
    
    public void GameRetry()
    {
        SceneManager.LoadScene(0);
    }
}
```



## 적 스크립트

```c#
public class Enemy : MonoBehaviour
{
    public string enemyName;
    public int enemyScore;
    public float speed;
    public int health;
    public Sprite[] sprites;
    
    public float maxShotDelay;
    public float curShotDelay;
    
    public GameObject bulletObjA;
    public GameObject bulletObjB;
    public GameObject itemCoin;
    public GameObject itemPower;
    public GameObject itemBoom;
    public GameObject player;
    public ObjectManager objectManager;
    
    SpriteRenderer spriteRenderer;
   
    
    void Awake()
    {
        spriteRenderer = GetComponent<SpriteRenderer>();
	}
    
    void OnEnable()
    {
        switch (enemyName)
        {
            case "L":
                health = 40;
                break;
            case "M":
                health = 10;
                break;
            case "S":
                health = 3;
                break;
        }
    }
    
    void Update()
    {
        Fire();
        Reload();
	}
    
    void Fire()
    {
        
        if(curShotDelay < maxShotDelay)
        	return;
        
        if(enemyName == "S")
        {
            GameObject bullet = objectManager.MakeObj("BulletEnemyA");
            bullet.transform.position = transform.position - transform.position;
            
            Rigidbody2D rigid = bullet.GetComponent<Rigidbody2D>();
            Vector3 dirVec = player.transform.position - transform.position;
            rigid.AddForce(dirVec.normalized * 3, ForceMode2D.Impulse);
		}
        else if(enemyName == "L")
        {
            GameObject bulletR = objectManager.MakeObj("BulletEnemyB");
            bullet.transform.position = transform.position + Vector3.right * 0.3f;
            GameObject bulletL = objectManager.MakeObj("BulletEnemyB");
            bullet.transform.position = transform.position + Vector3.left * 0.3f;
            
            Rigidbody2D rigidR = bulletR. GetComponent<Rigidbody2D>();
            Rigidbody2D rigidL = bulletL.GetComponent<Rigidbody2D>();
            
            Vector3 dirVecR = player.transform.position - (transfor.position + Vector3.right * 0.3f);
            Vector3 dirVecL = player.transform.position - (transfor.position + Vector3.left * 0.3f);
            
            rigidR.AddForce(dirVecR.normalized * 10, ForceMode2D.Impulse);
            rigidL.AddForce(dirVecL.normalized * 10, ForceMode2D.Impulse);
		}
        curShotDelay = 0;
	}
    
    void Reload()
    {
        curShotDelay += Time.deltaTime;
        
	}
    
    public void OnHit(int dmg)
    {
        if(health <= 0)
            return;
        
        health -= dmg;
        spriteRenderer.sprite = sprites[1]; // 피격 당했을 때 색깔 바뀌는 것을 표현
        Invoke("ReturnSprite", 0.1f);
        
        if(health <= 0)
        {
            Player playerLogic = player.GetComponent<Player>();
            playerLogic.score ++ enemyScore; // 적이 죽을 때 플레이어 점수에 더해줌
            
            // #.Random Ratio Item Drop
            int ran = Random.Range(0, 10);
            if(ran < 5)
            {
                Debug.Log("Not Item");
            }
            else if(ran < 6) // Coin
            {
                GameObject itemCoin = objectManager.MakeObj("ItemCoin");
                itemCoin.transform.position = transform.position;
            }
             else if(ran < 8) // Power
            {
                GameObject itemPower = objectManager.MakeObj("ItemPower");
             	itemPower.transform.position = transform.position;
            }
             else if(ran < 10) // Boom
            {
                GameObject itemBoom = objectManager.MakeObj("ItemBoom");
                 itemBoom.transform.position = transform.position;
            }
            gameObject.SetActive(false); // Destory()에서 바꿈
            transform.rotation = Quternion.identity;
		}
	}
    void ReturnSprite()
    {
        spriteRenderer.sprite = sprites[0];
	}
    
    void OnTriggerEnter2D(Collider2D collision)
    {
        if(collision.gameObject.tag == "BorderBullet")
        {
            gameObject.SetActive(false);// 비활성화
            transform.rotation = Quternion.identity;
        }	
        else if(collision.gameObject.tag == "PlayerBullet")
        {
            Bullet bullet = collision.gameObject.GetComponent<Bullet>();
                OnHit(bullet.dmg);
            
            gameObject.SetActive(false);
        }  
    }
}
```

Quternion.identity : 기본 회전값 = 0

OnEnable() : 컴포넌트가 활성화 될 때 호출되는 생명주기함수



## 플레이어 스크립트

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
            case 3;
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
                    	power++;
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



## 아이템 스크립트

```c#
public class Item : MonoBehaviour
{
    public string type;
    Rigidbody2D rigid;
    
    void Awake()
    {
        rigid = GetComponent<Rigidbody2D>();
    }
    
    void OnEnable()
    {
        rigid.velocity = Vector2.down * 1.5f;
    }
}
```

