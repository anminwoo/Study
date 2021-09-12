### 적 스폰 강화

스폰 포인트 복사 해서 좌우로 배치한다.

```c#
public class GameManager : MonoBehaviour
{
    public GameObject[] enemyObjs;
    public Transform[] spawnPoints;
    
    public float maxSpawnDelay;
    public float curSpawnDelay;
    
    public GameObject player;
    
    void Update()
    {
        curSpawnDelay == Time.deltaTime;
        
        if(curSpawnDelay > maxSpawnDelay)
        {
            SpawnDelay();
            maxSpawnDelay = Random.Range(0.5f, 3f);
            SpawnDelay = 0; // 적 생성 후 딜레이 변수 0으로 초기화
        }
    }
    
    void SpawnEnemy()
    {
        int ranEnemy = Random.Range(0, 3);
        int ranPoint = Random.Range(0, 5); // 이부분은 자신이 배치한 위치로 바꿔줘야함
       GameObject enemy = Instantiate(emenyObjs[ranEnemy], spawnPoints[ranPoint].position, 
                    					 spawnPoints[ranPoint].rotation);
        Rigidbody2D rigid = enemy.GetComponent<Rigidbody2D>();
        Enemy enemyLogic = enemy.GetComponent<Enemy>();
        enemyLogic.player = player; // 적 생성 직후 플레이어 변수를 넘겨줌
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
    
    public void RespawnPlayer()
    {
        Invoke("RespawnPlayerExe", 2f);
    }
    
	void RespawnPlayerExe()
    {
        Invoke()
        player.transform.position = Vector3.down * 3.5f;
        player.SetActive(true);
    }
}
```

```c#
public class Enemy : MonoBehaviour
{
    public string enemyName;
    public float speed;
    public int health;
    public Sprite[] sprites;
    
    public float maxShotDelay;
    public float curShotDelay;
    
    public GameObject bulletObjA;
    public GameObject bulletObjB;
    public GameObject player;
    
    SpriteRenderer spriteRenderer;
   
    
    void Awake()
    {
        spriteRenderer = GetComponent<SpriteRenderer>();
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
            GameObject bullet = Instantiate(bulletObjA, transform.position, transform.rotation);
            
            Vector3 dirVec = player.transform.position - transform.position;
            rigid.AddForce(dirVec.normalized * 3, ForceMode2D.Impulse);
        		Rigidbody2D rigid = bullet.GetComponent<Rigidbody2D>();
       			rigid.AddForce(Vector2.down*10, ForceMode2D, Impulse);
		}
        else if(enemyName == "L")
        {
            GameObject bulletR = Instantiate(bulletObjB, transform.position + Vector3.right * 0.3f, transform.rotation);
            GameObject bulletL = Instantiate(bulletObjB, transform.position + Vector3.left * 0.3f, transform.rotation);
            
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
    
    void OnHit(int dmg)
    {
        health -= dmg;
        spriteRenderer.sprite = sprites[1]; // 피격 당했을 때 색깔 바뀌는 것을 표현
        Invoke("ReturnSprite", 0.1f);
        
        if(health <= 0)
        {
            Destroy(gameObject);
		}
	}
    void ReturnSprite()
    {
        spriteRenderer.sprite = sprites[0];
	}
    
    void OnTriggerEnter2D(Collider2D collision)
    {
        if(collision.gameObject.tag == "BorderBullet")
            Destroy(gameObject);// 플레이어 화면 밖으로 나간 후에는 삭제시킴
        else if(collision.gameObject.tag == "PlayerBullet")
        {
            Bullet bullet = collision.gameObject.GetComponent<Bullet>();
                OnHit(bullet.dmg);
            
            Destroy(collision.gameObject);
        }  
    }
}
```



Border Bullet 조절해서 생성에 문제가 없도록 해야한다.

총알 프리펩을 복사 -> Open Prefab 기능으로 수정

이름을 Enemy Bullet A 원하는 스프라이트로 바꾸기

스프라이트 크기 만한 콜라이더를 만들어줌

Enemy Bullet B로 이름을 바꾸고 스프라이트를 바꾸기

circle collider 넣기

Tag에 EnemyBullet 태그 만들기

프리펩은 이미 Scene에 올라온 오브젝트에 <a style = "color : red">접근 불가능</a>

GameManager Player에 Player 넣기

프리펩이 프리펩 사용은 가능하다.

총알 프리펩 넣어주기

Enemy Name 써주기

normalized : 벡터가 단위 값(1)로 변환된 변수



### 플레이어 피격 이벤트

```c#
public class Player : MonoBehaviour
{
    public float speed;
    public bool isTouchTop;
    public bool isTouchBottom;
    public bool isTouchRight;
    public bool isTouchLeft;
    
    public float speed;
    public float power;
    public float maxShotDelay; // 최대
    public float curShotDelay; // 현재
    
    public GameObject bulletObjA; // 총알 프리렙 저장
    public GameObject bulletObjB;
    
    public GameManager manager;
    Animator anim;
    
    void Awake()
    {
        anim = GetCompoent<Animator>();
	}
    
    void Update()
    {
        Move();
        Fire();
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
        		GameObject bullet = Instantiate(bulletObjA, transform.position, transform.rotation);
        		Rigidbody2D rigid = bullet.GetComponent<Rigidbody2D>();
       			rigid.AddForce(Vector2.up*10, ForceMode2D, Impulse);
                break;
        	case 2; // 두발 발사 될 때
                GameObject bulletR = Instantiate(bulletObjA, transform.position + vector3.right * 0.1f, 					transform.rotation);
                GameObject bulletL = Instantiate(bulletObjA, transform.position + vector3.left * 0.1f, 						transform.rotation);
       			Rigidbody2D rigid = bullet.GetComponent<Rigidbody2D>();
                Rigidbody2D rigid = bullet.GetComponent<Rigidbody2D>();
      			rigid.AddForce(Vector2.up*10, ForceMode2D, Impulse);
                rigid.AddForce(Vector2.up*10, ForceMode2D, Impulse);
                break;
            case 3;
        		GameObject bulletR = Instantiate(bulletObjA, transform.position + vector3.right * 0.35f, 						transform.rotation); // 오른쪽
                GameObject bulletC = Instantiate(bulletObjB, trnasform.position, transform.rotation); // 중앙
                GameObject bulletL = Instantiate(bulletObjA, transform.position + vector3.left * 0.35f, 						transform.rotation); // 왼쪽
       			Rigidbody2D rigidR = bullet.GetComponent<Rigidbody2D>();
                Rigidbody2D rigidC = bullet.GetComponent<Rigidbody2D>();
                Rigidbody2D rigidL = bullet.GetComponent<Rigidbody2D>();                                 
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
            manager.RespawnPlayer();
            gameObject.SetActive(false);
        }
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

