슈팅게임 보스 구현

Pixel Per Unit : 24

Sprite Mode : Multiple

Compression : None



Enemy B

스프라이트 이어서 애니메이션 파일 안에 Boss_Idle로 저장 

스파라이트 색깔 변하는 거  Boss_Hit로 저장

캡슐 콜라이더의 모양은 가로 방향으로 바꿀 수 있다.

Tag : Enemy

기본 적 총알을 복사하여 보스 총알로 변경

원하는 스프라이트 선택 Y축 전환 

Box Colider size 총알 프리펩에 맞도록 조정

Enemy Bullet D



총알 하나 더 만들기 Scale, Box Colider 조정해서 만들기



### 오브젝트 매니저 스크립트

```c#
public class ObjectManager : MonoBehaviour
{
    public GameObject enemyBPrefab;
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
    public GameObject bulletBossAPrefab;
    public GameObject bulletBossBPrefab;
    
    GameObject[] enemyB;
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
    GameObject[] bulletBossA;
    GameObject[] bulletBossB;
    
    GameObject[] targetPool;
    
    void Awake()
    {
        enemyB = new GameObject
        enemyL = new GameObject[1]; // 한번에 등장할 개수를
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
        bulletBossA = new GameOBject[50];
        bulletBossB = new GameOBject[1000];
        
        Generate();
    }
    
    void Generate()
    {
        // #1. Enemy
        
        for(int index = 0; index < enemyB.Length; index++)
        {
            enemyB[index] = Instantiate(enemyBPrefab);
            enemyB[index].SetActive(false);
        }
        
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
        for(int index = 0; index < bulletPlayerA.Length; index++)
        {
            bulletPlayerA[index] = Instantiate(bulletPlayerAPrefab);
            bulletPlayerA[index].SetActive(false);
        }
         
        for(int index = 0; index < bulletPlayerB.Length; index++)
        {
            bulletPlayerB[index] = Instantiate(bulletPlayerBPrefab);
            bulletPlayerB[index].SetActive(false);
        }
        
        for(int index = 0; index < bulletEnemyA.Length; index++)
        {
            bulletEnemyA[index] = Instantiate(bulletEnemyAPrefab);
            bulletEnemyA[index].SetActive(false);
        }
            
        for(int index = 0; index < bulletEnemyB.Length; index++)
        {
            bulletEnemyB[index] = Instantiate(bulletEnemyBPrefab);
        bulletEnemyB[index].SetActive(false);
        }
        
        for(int index = 0; index < bulletFollower.Length; index++)
        {
            bulletFollower[index] = Instantiate(bulletFollowerPrefab);
            bulletFollower[index].SetActive(false);
        }
        
        for(int index = 0; index < bulletBossA.Length; index++)
        {
            bulletBossA[index] = Instantiate(bulletBossAPrefab);
            bulletBossA[index].SetActive(false);
        }
        
        for(int index = 0; index < bulletBossB.Length; index++)
        {
            bulletBossB[index] = Instantiate(bulletBossBPrefab);
            bulletBossB[index].SetActive(false);
        }
    }
    
    public GameObject MakeObj(string type)
    {
        switch (type)
        {
            case "EnemyB":
                targetPool = enemyB;
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
            case "BulletBossA":
                targetPool = bulletBulletA;
                break;
            case "BulletBossB":
                targetPool = bulletBulletB;
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
            case "EnemyB":
                targetPool = enemyB;
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
            case "BulletBossA":
                targetPool = bulletBulletA;
                break;
            case "BulletBossB":
                targetPool = bulletBulletB;
                break;
        }
        
        return targetPool;
    }
}
```

보스 총알 프리펩 넣어주기

Is Trigger 옵션 체크해주기



### 보스 패턴 스크립트

```c#
public class Bullet : MonoBehaviour
{
    public int dmg;
    public bool isRotate;
    
    void Update()
    {
        if(isRotate)
            transform.Rotate(Vector3.foward * 10);
    }
    
    void OnTriggerEnter2D(Collider2D collision)
    {
        if(collision.gameObject.tag == "BorderBullet")
        {
            gameObject.SetActive(false);
        }
    }
}
```

Enemy 스크립트 재활용



### Enemy 스크립트

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
   Animator anim;
    
    public int patternIndex;
    public int curPatternCount;
    public int[] maxPatternCount;
    
    void Awake()
    {
        spriteRenderer = GetComponent<SpriteRenderer>();
        
        if(enemyName == "B")
            anim = GetComponent<Animator>();
	}
    
    void OnEnable()
    {
        switch (enemyName)
        {
            case "B":
                health = 3000;
                Invoke("Stop", 2);
                break;
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
    
    void Stop()
    {
        if(gameObject.activeSelf)
            return;
        
        Rigidbody2D rigid = GetComponent<Rigidbody2D>();
        rigid.velocity = Vector2.zero;
        
        Invoke("Think", 2);
    }
    
    void Think()
    {
        patternIndex = patternIndex == 3 ? 0 : patternIndex + 1; // 현재 패턴이 패턴 갯수를 넘기면 0으로 돌아옴
        courPatternCount = 0; // 패턴 변경을 할 때 마다 실행 횟수 변수 초기화
        
        switch(patternIndex)
        {
            case 0:
            	FireFoward();
                break;
            case 1:
                FireShot();
                break;
            case 2:
                FireArc();
                break;
            case 3:
                FireAround();
                break;    
        }
    }
    
    void FireFoward()
    {
        // #. Fire 4 Bullet Foward
        GameObject bulletR = objectManager.MakeObj("BulletBossA");
            bullet.transform.position = transform.position + Vector3.right * 0.3f;
         GameObject bulletRR = objectManager.MakeObj("BulletBossA");
            bullet.transform.position = transform.position + Vector3.right * 0.45f;
            GameObject bulletL = objectManager.MakeObj("BulletBossA");
            bullet.transform.position = transform.position + Vector3.left * 0.3f;
        GameObject bulletLL = objectManager.MakeObj("BulletBossA");
            bullet.transform.position = transform.position + Vector3.left * 0.45f;
            
        Rigidbody2D rigidR = bulletR. GetComponent<Rigidbody2D>();
        Rigidbody2D rigidRR = bulletR. GetComponent<Rigidbody2D>();
        Rigidbody2D rigidL = bulletL.GetComponent<Rigidbody2D>();
        Rigidbody2D rigidLL = bulletL.GetComponent<Rigidbody2D>();
            
            Vector3 dirVecR = player.transform.position - (transfor.position + Vector3.right * 0.3f);
            Vector3 dirVecL = player.transform.position - (transfor.position + Vector3.left * 0.3f);
            
        rigidR.AddForce(Vector2.down * 8, ForceMode2D.Impulse);
		rigidRR.AddForce(Vector2.down * 8, ForceMode2D.Impulse);
        rigidL.AddForce(Vector2.down * 8, ForceMode2D.Impulse);
        rigidLL.AddForce(Vector2.down * 8, ForceMode2D.Impulse);
        
        // #. Pattern Counting
        curPatternCount++;
        // 패턴별 횟수를 실행하고 다음 패턴으로 넘어감
        
        if(curPatternCount < maxPatternCount[patternIndex])
            Invoke("FireFoward", 2);
        else
        	Invoke("Think", 3);
    }
    
    void FireShot()
    {
        for(int index = 0; index < 5; index++)
        {
            // #. Fire 5 Random Shotgun Bullet to Player
            GameObject bullet = objectManager.MakeObj("BulletEnemyB");
            bullet.transform.position = transform.position - transform.position;
            
         Rigidbody2D rigid = bullet.GetComponent<Rigidbody2D>();
         Vector2 dirVec = player.transform.position - transform.position;
            Vector2 ranVec = new Vector2(Random.Range(-0.5f, 0.5f), Random.Range(0f, 2f));
            dirVec += ranVec;
         rigid.AddForce(dirVec.normalized * 3, ForceMode2D.Impulse);
        }
        
        // #. Pattern Counting
       	curPatternCount++;
        
        if(curPatternCount < maxPatternCount[patternIndex])
            Invoke("FireShot", 3.5f);
       else
           Invoke("Think", 3);
    }
    
    void FireArc()
    {
        // #. Fire Arc Continue Fire
        GameObject bullet = objectManager.MakeObj("BulletEnemyA");
        bullet.transform.position = transform.position - transform.position;
            bullet.transform.rotation = Quaternion.identity;
            
         Rigidbody2D rigid = bullet.GetComponent<Rigidbody2D>();
         Vector2 dirVec = new Vector2(Mathf.Sin(Mathf.PI * 10 * curPatternCount / maxPatternCount[patternIndex]), -1);
         rigid.AddForce(dirVec.normalized * 3, ForceMode2D.Impulse);
        }
        
    // #.Pattern Counting
        curPatternCount++;
        
        if(curPatternCount < maxPatternCount[patternIndex])
            Invoke("FireArc", 0.15f);
       else
           Invoke("Think", 3);
    }
    
    void FireAround()
    {
        // #. Fire Around
        int roundNumA = 50;
        int roundNumB = 40;
        int roundNum = courPatternCount % 2 == 0 ? roundNumA : roundNumB;
        
        for(int index = 0; index < roundNum; index++)
        {
            GameObject bullet = objectManager.MakeObj("BulletEnemyB");
        	bullet.transform.position = transform.position - transform.position;
        	bullet.transform.rotation = Quaternion.identity;
            
            Rigidbody2D rigid = bullet.GetComponent<Rigidbody2D>();
         Vector2 dirVec = new Vector2(Mathf.Cos(Mathf.PI * 2 * index / roundNum), -1);
            ,Mathf.Sin(Mathf.PI * 2 * index / roundNum), -1);
         rigid.AddForce(dirVec.normalized * 2, ForceMode2D.Impulse);
            
            Vector3 rotVec = Vector3.forward * 360 * index / roundNumA + Vector3.forward * 90;
            bullet.transform.Rotate(rotVec);
        }
        curPatternCount++;
        
        if(curPatternCount < maxPatternCount[patternIndex])
            Invoke("FireAround", 0.7f);
       else
           Invoke("Think", 3);
    }
    
    void Update()
    {
        if(enemyName == "B") // 보스면 return
            return;
        
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
        if(enemyName == "B")
        {
            anim.SetTrigger("OnHit");
        }
        else
        {
            spriteRenderer.sprite = sprites[1]; // 피격 당했을 때 색깔 바뀌는 것을 표현
        	Invoke("ReturnSprite", 0.1f);
        }
        
        
        if(health <= 0)
        {
            Player playerLogic = player.GetComponent<Player>();
            playerLogic.score ++ enemyScore; // 적이 죽을 때 플레이어 점수에 더해줌
            
            // #.Random Ratio Item Drop
            int ran = enemyName == "B" ? 0 : Random.Range(0, 10);
            
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
        if(collision.gameObject.tag == "BorderBullet" && enemyName != "B")
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

캡슐 콜라이더 넣어주기 (원하는 크기로 하면 됨 이 강의에서는 X : 2.7 Y : 1.5로 했음)

발사 횟수를 홀수로 두면 플레이어의 이동이 강제된다.

### 게임 매니저 스크립트

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
        enemyObjs = new string[] { "EnemyS", "EnemyM", "EnemyL", "EnemyB" };
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
        int enemyIndex = 0;
        switch(spawnList[spawnIndex].type)
        {
            case "S":
                enemyIndex = 0;
                break;
            case "M":
                enemyIndex = 1;
                break;
            case "L":
                enemyIndex = 2;
                break;
            case "B":
                enemyIndex = 3;
                break;
        }
        int enemyPoint = spawnList[spawnIndex].point;
       GameObject enemy = objectManager.MakeObj(enemyObjs[enemyIndex]);
        enemy.transform.position = spawnPoints[enemyPoint].position;
        
        Rigidbody2D rigid = enemy.GetComponent<Rigidbody2D>();
        Enemy enemyLogic = enemy.GetComponent<Enemy>();
        enemyLogic.player = player; // 적 생성 직후 플레이어 변수를 넘겨줌
        enemyLogic.objectManager = objectManager;
        if(enemyPoint == 5 || enemyPoint == 6) // Right Spawn
        {
            enemy.transform.Rotate(Vector3.back * 90); // z축 단일
            rigid.velocity = new Vector2(enemyLogic.speed*(-1), -1);
		}
        else if(enemyPoint == 7 || enemyPoint == 8) // Left Spawn
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
