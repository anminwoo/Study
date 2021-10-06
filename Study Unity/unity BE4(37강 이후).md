모바일 슈팅게임 만들기



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
    public bool isRespawnTime;
    
    public bool[] joyControl;
    public bool isControl;
    public bool isButtonA;
    public bool isButtonB;
    
    Animator anim;
    SpriteRenderer spriteRenderer
    
    void Awake()
    {
        anim = GetCompoent<Animator>();
        spriteRenderer = GetComponent<SpriteRenderer>();
	}
    
    void OnEnable()
    {
        Unbeatable();
        Invoke("Unbeatable", 3);
    }
    
    void Unbeatable()
    {
        isRespawnTime = !isRespawnTime;
        if(isRespawnTime) // #. 무적 타임 이펙트 (투명)
        {
        	isRespawnTime = true;
            spriteRenderer.color = new Color(1,1,1,0.5f);
            
            for(int index = 0; index < followers.Length; index++)
            {
                followers[index].GetComponent<SpriteRenderer>().color = new Color(1, 1, 1, 0.5f);
            }
        }
        else  // #. 무적 타임 이펙트 종료 (원래대로)
        {
            isRespawnTime = false;
            spriteRenderer.color = new Color(1,1,1,1);
            
            for(int index = 0; index < followers.Length; index++)
            {
                followers[index].GetComponent<SpriteRenderer>().color = new Color(1, 1, 1, 1);
            }
        }
    }
    
    void Update()
    {
        Move();
        Fire();
        Boom();
        Reload();
	}
    
    public void JoyPanel(int type)
    {
        for(int index = 0; index < 9; index++)
        {
            joyControl[index] = index == type;
        }
    }
    
    public void JoyDown()
    {
        isControl = true;
    }
    
    public void JoyUp;
    {
    	isControl = false;   
    }
    
    void Move()
    {
        // #. Keyboard Control Value
        float h = Input.GetAxisRaw("Horizontal"); // 가로
        float v = Input.GetAxisRaw("Vertical"); // 세로
        
        // #. Joy Control Value
        if(joyControl[0]) { h = -1; v = 1; } // 방향 버튼 변수에 따라 수평, 수직 값 적용
        if(joyControl[1]) { h = 0; v = 1; }
        if(joyControl[2]) { h = 1; v = 1; }
        if(joyControl[3]) { h = -1; v = 0; }
        if(joyControl[4]) { h = 0; v = 0; }
        if(joyControl[5]) { h = 1; v = 0; }
        if(joyControl[6]) { h = -1; v = -1; }
        if(joyControl[7]) { h = 0; v = -1; }
        if(joyControl[8]) { h = 1; v = -1; }
        
        if((isTouchRight && h == 1) || (isTouchLeft && h == -1) || !isControl)
            h = 0;
        
        if((isTouchTop &&v == 1) || (isTouchBottom && v ==  - 1) || !isControl)
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
    
    public void ButtonADown()
    {
        isButtonA = true;
    }
    
     public void ButtonAUp()
     {
        isButtonA = false; 
     }
    
    public void ButtonBDown()
    {
        isButtonB = true;
    }
    
    void Fire()
    {
         // if(!Input.GetButton("Fire1"))
         //     return;
        
        if(!isButtonA)
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
       // if(!Input.GetButton("Fire2"))
       //    return;
        
        if(!isButtonB)
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
            if(isRespawnTime)
                 return;
            
            if(isHit) // 총알 두개를 동시에 맞았을 때 한번에 목숨 2개가 깎이는 것을 방지함
                return;
            life--;
            gameManager.UpdateLifeIcon(life);
            gameManager.CallExplosion(transform.position, "P"); // GameManager에서 생성한 폭발 함수를 플레이어, 적에서 호출한다.
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

보조무기(Follower)를 플레이어 자식 오브젝트로 넣어서 같이 활성화/비활성화가 되게 만들어준다.



폭팔 효과

다운로드 후

Sprite Mode : Multiple

PIxel Per Unit : 24

Compression : None



Pixel Size X : 24 Y : 24

Padding X : 1 Y : 1

설정 하고

폭발 스프라이트 애니메이션 6장 선택 후

Animation 파일 안에 Explosion 이라는 이름으로 저장



빈 State를 Idle로 이름을 바꾸고 Set as Layer Default State로 설정해준다. 

Any State -> Explosion -> Idle 연결해주기

OnExplosion 이라는 이름으로 Trigger 만들기

Settings에 있는 Transition Duration : 0 으로 설정한다. (Idle, Explosion)

스프라이트 속도를 확인해보고 speed를 조절해준다.



###  Explosion 스크립트

```c#
public class Explosion : MonoBehaviour
{
    Animator anim;
    
    void Awake()
    {
        anim = GetComponent<Animator>();
    }
    
    void OnEnable()
    {
        Invoke("Disable", 2f);
    }
    
    void Disable()
    {
        gameOBject.SetActive(false);
    }
    
    public void StartExplosion(string target) // 크기에 따라 폭발 효과 크기를 설정함
    {
        anim.SetTrigger("OnExplosion");
        
        switch (target)
        {
            
            case "S":
                tarnsform.localScale = Vector3.one * 0.7f;
                break;
            case "M":
            case "P": // 플레이어와 M(중간 크기 적) 크기가 비슷하므로 함께 묶어놓음
                tarnsform.localScale = Vector3.one * 1f;
                break;
            case "L":
                tarnsform.localScale = Vector3.one * 2f;
                break;
            case "B":
                tarnsform.localScale = Vector3.one * 3f;
                break;
		}
    }
}
```



프리펩으로 저장



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
    public GameObject explosionPrefab;
    
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
    GameObject[]  explosion;
    
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
        explosion = new GameObject[20];
        
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
        
        for(int index = 0; index < explosion.Length; index++)
        {
            explosion[index] = Instantiate(explosionPrefab);
            explosion[index].SetActive(false);
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
            case "Explosion":
                targetPool = explosion;
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
            case "Explosion":
                targetPool = explosion;
                break;
        }
        
        return targetPool;
    }
}
```





### 게임 매니저 스크립트

```c#
using UnityEngine.UI // UI를 사용하기 위해
using UnityEngine.SceneManagement // 재시작하기 위해
public class GameManager : MonoBehaviour
{
    public int stage;
    public Animator stageAnim;
    public Animator clearAnim;
    
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
    
    public List<Spawn> spawnList;
    public int spawnIndex;
    public bool spawnEnd;
    
    void Awake()
    {
        spawnLIst = new List<Spawn>();
        enemyObjs = new string[] { "EnemyS", "EnemyM", "EnemyL", "EnemyB" };
        StageStart();
    }
    
    public void StageStart()
    {
        // #. Stage UI Load
        stageAnim.SetTrigger("On");
        stageAnim.GetComponent<Text>().text = "Stage " + stage = "\nStart";
        clearAnim.GetComponent<Text>().text = "Stage " + stage = "\nClear!";
        // #. Enemy Spawn File Read
        ReadSpawnFile();
        // #. Fade In
    }
    
    public void StageEnd()
    {
        // #. Clear UI Load
        clearAnim.SetTrigger("On");
        
        // #. Stage Increment
        stage++;
        
        // #. Fade Out
        
        // #. Player Repos
	}
    
     void ReadSpawnFile()
    {
        // #1. 변수 초기화
        spawnList.Clear();
        spawnIndex = 0;
        spawnEnd = false;
        
        // #2. 리스폰 파일 읽기
        TextAsset textFile = Resources.Load("Stage " + stage) as TextAsset;
        StringReader stringReader = new StringReader(textFile.text);
        
         // #3. 한 줄씩 데이터 저장
        while(stringReader != null)
        {
            string line = stringReader.ReadLine();
            
            if(line == null)
                break;
            
            // #. 리스폰 데이터 생성
        Spawn spawnData = new Spawn();
        spawnData.delay = float.Parse(line.Split(',')[0]);
        spawnData.type = line.Split(',')[1];
        spawnData.point = int.Parse(line.Split(',')[2]);
        spawnList.Add(spawnData);
        }
        
        // #. 텍스트 파일 닫기
        stringReader.Close();
        
        // #. 첫 번째 스폰 딜레이 적용
       nextSpawnDelay = spawnList[0].delay;
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
       GameObject enemy = objectManag
           er.MakeObj(enemyObjs[enemyIndex]);
        enemy.transform.position = spawnPoints[enemyPoint].position;
        
        Rigidbody2D rigid = enemy.GetComponent<Rigidbody2D>();
        Enemy enemyLogic = enemy.GetComponent<Enemy>();
        enemyLogic.player = player; // 적 생성 직후 플레이어 변수를 넘겨줌
        enemyLogic.gameManager = this;
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
    
    public void CallExplosion(Vector3 pos, string type)
    {
        GameObject explosion = objectManager.MakeObj("Explosion");
        Explosion explosionLogic = explosion.GetComponent<>();
        
        explosion.transform.position = pos;
        explosionLogic.StartExplosion(type);
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

this : 클래스 자신을 일컫는 키워드



### 적 스크립트

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
    public GameManager gameManager;
    public ObjectManager objectManager;
    
    SpriteRenderer spriteRenderer;
   Animator anim;
    
    
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
                Stop();
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
            gameManager.CallExplosion(transform.position, enemyName);
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

프리펩 등록 꼭 시키기

Sprite Renderer에서 Sprite : None 으로 하기 (안그러면 스프라이트가 남아서 이상하게 보임)



스프라이트 다운로드 받고

UI_Set Import Settings 찾아



Sprite Mode : Multiple

Pixels Per Unit : 24

Filter Mode : Point (no filter)

Compression : None



랜더 모드를 Camera로 변경

Canvas - > Render Camera -> Render Camera에 Main Camera 넣기 

Order in Layer : 10

Pixel Perfect 켜주기



Canvas -> Ui -> Image 이름  Joy Panel로 바꿔주기

화살표가 그려져있는 UI를 Source Image에 넣어주기

Width : 300

Height : 300



Anchor Presets에서 shift + alt 누른 상태로 좌측 아래 선택

Pos X : 25, Pos Y : 25

이미지의 알파값을 절반정도로 줄여줌. (나중에 비행기가 컨트롤러 이미지 뒤에 가려져서 안보일 수 있음.)

Joy Panel 안에 버튼 하나 생성

이미지 안의 버튼은 이미지와 색상이 보이지 않게 설정

텍스트도 빼주기

Transition : None으로 바꿔주기

Width : 100, Height : 100

왼쪽 위로 앵커 설정해주기 

버튼 9개를 만들고 이름과 배치해주기

EventTrigger 추가하기

* Pointer Down, Pointer Up, Pointer Enter 추가하기
* 각각 '+' 를 눌러주기
* Object 라고 적혀있는 곳에 Player 넣어주고 각 버튼에 맞게 버튼 이벤트 넣어주기



키 맵핑 하기

* Pointer Enter에 0 넣어주기

* Copy Component 눌러주고 나머지 8개 선택 후 Paste Component Value 누르기

* Pointer Enter에서 각 버튼에 맞게 숫자 넣어주기

* | 0    | 1    | 2    |
  | ---- | ---- | ---- |
  | 3    | 4    | 5    |
  | 6    | 7    | 8    |



Joy Control에서 Size 9로 하기



발사 버튼 만들기 Set Native Size 눌러 버튼 보기 Width, Height를 200, 200 으로 놓기

앵커를 우측 하단으로 설정하기 Pos X, Pos Y를 -25, 25로 해서 여백주기 알파값을 낮춰 투명하게 만들어주기(170정도, 조이스틱도 170으로 동일)

폭탄버튼도 동일하게 하고 Width, Height 200, 150으로 하고 Pos X, Pos Y -25, 235로 하기

발사, 폭탄 버튼도 방향키와 동일하게 Event Trigger 넣고 (발사 버튼은 2개, 폭탄 버튼은 1개)

Add New Event Type 추가하고 None (Object)에 Player 넣어주기

각자 맞는 함수 넣어주기

Stage 0 번 복사 하기

Stage 0 삭제

 #### UI Text 생성

Start Text

Width : 700, Height : 300

Font : Bold

Font Size : 130

Clear Text도 똑같이 만들기

#### Text 넣어주기

Animation 파일에서 Animator Controller 만들기 이름 : Text

Animation 만들기 이름 : Text

애니메이터 넣어주기 (두개 다)

애니메이션 넣어주기

Create State -> Empty 만들기 이름 : Idle

Any State -> Text -> Idle 연결해주기

Trigger 넣어주기 이름 : On

Setting 에서 Transition Duration : 0으로 설정 (Any State -> Text, Text -> Idle 부분)

#### 애니메이션 효과

Window에서 Animation -> Animation (ctrl + 6으로도 가능)

아까 만들었던 Text 클릭

Rect Transform -> Scale 뒷부분 지우고 앞부분만 사용

키 프레임 추가

처음 설정은 0으로 0.3초 후에는 1.2

0.1초 후에는 1로 설정

살짝 오버 스케일 프레임을 넣어서 튀는 애니메이션을 연출한다.

5초에 키프레임 추가

다시 0.1 초 동안 다시 1.2

0..3초 뒤 0으로 설정

기본 스케일을 0, 0, 0으로 설정하기



#### 게임 매니저에 넣어주기

Stage 1로 바꿔주기

Stage Anim, Clear Anim에 Stage Text (Animator), Clear Text (Animator) 넣어주기



트랜지션의 겹치는 부분이 최대한 없도록 조정한다.



57분 이후부터 듣기