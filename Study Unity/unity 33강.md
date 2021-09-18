### 배경 만들기

Pixels Per Unit : 24

Filter Mode : Point(no filter)

Compression : None

각 스프라이트에 이름을 만들고 넣어준다.

Order Layer로 순서를 조절해준다.

그룹을 만들고 각자 3개씩 스프라이트 오브젝트를 생성한다.

-> 배경을 움직여 움직이는 것 처럼 보이게 하기 위해서

Camera View  높이 = Size * 2

### 배경 스크립트

```c#
public class Background : MonoBehaviour
{
    public float speed;
    public int startIndex;
    public int endIndex;
    public Transform[] sprites;
    
    float viewHeight;
    
    private void Awake()
    {
        viewHeight = Camera.main.orthographicSize * 2;
    }
    void Update()
    {
        Move();
        Scrolling();
    }
    
    void Move()
    {
        Vector3 curPos = transform.position;
        Vector3 nextPos = Vector3.down * speed * Time.deltaTime;
        transform.position = curPos + nextPos;
    }
    
    void Scrolling()
    {
        if(sprites[endIndex].positon.y < viewHeight * (-1))
        {
            // #.Sprite ReUse
            Vector3 backSpritePos = sprites[startIndex].localPosition;
            Vector3 forntSpritePos = sprites[endIndex].localPosition;
            sprites[endIndex].transform.localPosition = backSpritePos + Vector3.up * 10;
            
            // #.Cursor Index Change
            int startIndexSave = startIndex;
            startIndex = endIndex;
            endIndex = startIndexSave - 1 == -1 ? sprites.Length - 1; // 배열을 넘어가지 않도록 예외 처리
        }
    }
}
```

orthographicSize : orthographic 카메라 Size

스크립트 넣을 때 자물쇠 모양 누르고, 배열 이름에 들어갈 오브젝트를 모두 드래그 하면 자동으로 할당된다.

배경을 A, B, C 3개로 나누어 놓은 이유 : Parallax 사용 -> A, B, C의 속도를 다르게 해서 (가까이 있는 것은 빠르게, 멀리 있는 것은 느리게) 자연스러운 배경을 만들 수있다.

Parallax : 거리에 따른 상대적 속도를 활용한 기술

플랫포머 에서도 많이 사용함