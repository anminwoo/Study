## 적 비행기 만들기

* 트리거 이벤트를 위해서 콜라이더를 생성한다.

  * box collider *사각형 모양 물체*
  * polygon collider *삼각형 모양 물체*
  * circle collider *원 모양 물체*

* 스프라이트는 기본적으로 <a style = "color : #4e82fa">Physics Shape</a>가 있다.

  <a style = "color : yellowgreen">폴리곤 콜라이더</a>는 <a style = "color : #4e82fa">Physics Shape</a>를 따라감. -> <a style = "color : #4e82fa">Physics Shape</a>를 조정해서 원하는 콜라이더 모양을 만든다.

   

* Rigidbody의 Gravuty Scale 값은 0으로 설정한다.
* 적비행기에게 새로운 **태그**를 설정해준다.
* 스크립트를 만들고 적에게 넣어준다.

```c#
public class Enemy : MonoBehaviour
{
    public float speed;
    public int health;
    public Sprite[] sprites;
    
    SpriteRenderer spriteRenderer;
    Rigidbody2D rigid;
    
    void Awake()
    {
        spriteRenderer = GetComponent<SpriteRenderer>();
        rigid =  GetComponent<Rigidbody2D>();
        rigid.velocity = Vector2.down * speed; // velocity 값을 바로 속도로
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

Inspector 창에서 Player Bullet A, B에 PlayerBullet 태그를 만들어 넣어준다.



Bullet 컴포넌트

```c#
public class Bullet : MonoBehaviour    
{
    public int dmg;
    
    void OnTriggerEnter2D(Collider2D collision)
    {
        if(collision.gameObject.tag == "BorderBullet")
        {
            Destroy(gameObject);
        }
    }
}
```

총알, 체력 , 속도 설정하기

스프라이트 갯수 설정하기(여기선 2개 )

적끼리 충돌을 막기 위해 콜라이더의 Is Trigger 옵션 체크

프리펩 저장 후 위치 초기화



적 생성 관리 GameManager 생성

```c#
public class GameManager : MonoBehaviour
{
    public GameObject[] enemyObjs;
    public Transform[] spawnPoints;
    
    public float maxSpawnDelay;
    public float curSpawnDelay;
    
    void Updat()
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
        int ranPoint = Random.Range(0, 5);
        Instantiate(emenyObjs[ranEnemy], spawnPoints[ranPoint].position, 
                    					 spawnPoints[ranPoint].rotation);
    }
}
```

Range() : 정해진 범위 내의 랜덤 숫자 반환 (float, int)

Range() 함수는 매개변수에 의해 반환 타입이 결정된다.

만든 public 변수를 지정한다.

Enemy Objs : 적 종류

Enemy Objs : 스폰 포인트

* 빈 오브젝트 생성 -> 알아볼 수 있도록 이름 짓기(이강의에선 Spawn Point Group) -> 그안에 빈 오브젝트 스폰 포인트 갯수 만큼 생성 -> 삭제되는 경계선 안쪽에 만들기
  * 빈 오브젝트 확인하는 법 : Inspector 창에서 정육면체 모양 클릭 -> 태그 아이콘으로 확인할 수 있음

Enemy Objs, Enemy Objs에 프리펩 , 적 스폰 포인트 넣어주기