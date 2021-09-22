스크립트 하나 만들기 -> 이름 : Spawn

### Spawn 스크립트

Monobehavior 상속 제거

```c#
public class Spawn
{
    public float delay;
    public string type;
    public int point;
}
```



윈도우 메모장 실행

### 메모장

구조체 변수에 맞도록 값 입력 (+ 구분자)

```
1,S,1
0.2,S,1
0.2,S,1
0.2,S,1
0.2,S,1
1,S,3
0.2,S,3
0.2,S,3
0.2,S,3
0.2,S,3
2,M,2

1,S,1
0.4,S,1
0.4,S,1
0.4,S,1
0.4,S,1
1,S,3
0.4,S,3
0.4,S,3
0.4,S,3
0.4,S,3
2.5,M,2
0,M,0
0,M,4
1,S,7
0.4,S,7
0.4,S,5
3,L,3
```

원하는 이름으로 저장 (강의에서는 Stage0로 저장함)



Resources 폴더 생성 -> 아까 메모장 파일 넣기

Resources : 런타임에서 불러오는 에셋이 저장된 폴더



### GameManager 스크립트

TextAsset : 텍스트 파일 에셋 클래스

Resources.Load() : Resources 폴더 내 파일 불러오기

StringReader : 파일 내의 문자열 데이터 읽기 클래스

ReadLine() : 텍스트 데이터를 한 줄씩 반환. (자동 줄 바꿈)

Split() : 지정한 구분 문자로 문자열을 나누는 함수

```c#
using UnityEngine.UI // UI를 사용하기 위해
using UnityEngine.SceneManagement // 재시작하기 위해
using System.IO; // 파일을 읽기 위해

public class GameManager : MonoBehaviour
{
    public string[] enemyObjs;
    public Transform[] spawnPoints;
    
    public floatnextSpawnDelay;
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
        spawnList = new List<Spawn>();
        enemyObjs = new string[] { "EnemyS", "EnemyM", "EnemyL" };
        ReadSpawnFile();
    }
    
    void ReadSpawnFile()
    {
        // #1. 변수 초기화
        spawnList.Clear();
        spawnIndex = 0;
        spawnEnd = false;
        
        // #2. 리스폰 파일 읽기
        TextAsset textFile = Resources.Load("Stage 0") as TextAsset;
        StringReader stringReader = new StringReader(textFile.text);
        
        while(stringReader != null)
        {
            string line = stringReader.ReadLine();
            Debug.Log(line);
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
        
        if(curSpawnDelay > nextSpawnDelay && !spawnEnd)
        {
            SpawnDelay();
            c
                urSpawnDelay = 0; // 적 생성 후 딜레이 변수 0으로 초기화
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
        }
        int enemyPoint = spawnList[spawnIndex].point;
       GameObject enemy = objectManager.MakeObj(enemyObjs[ranEnemy]);
        enemy.transform.position = spawnPoints[ranPoint].position;
        
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
        // #. 리스폰 인덱스 증가
        spawnIndex++;
        if(spawnIndex == spawnList.Count)
        {
            spawnEnd = true;
            return;
        }
        // #. 다음 리스폰 딜레이
        nextSpawnDelay = spawnList[spawnIndex].delay;
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

