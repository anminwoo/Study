Create Empty (이름 : Bullet HandGun)

총알 효과로 Trail Renderer 추가

Material에 Element 0을 Default-Line으로 설정, Time : 0, Min Vertex Distance : 0

Trail Renderer Width를 세로는 0.5, 가로는 0.7에 key 생성 후 곡선 형태가 되도록 당겨준다.

Color : (169, 222, 20) 부터 (255, 194, 0) 연두색 부분 화살표 Location 10.3%

끝부분 알파값을 50으로 해준다.

리지드 바디, 콜라이더(Sphere) 추가 위치 0, 0, 0으로 맞춰주기

Radius : 0.3



ctrl + d로 복사 후 Bullet SubMachineGun 

Trail Renderer에서 Time : 0.1

기관총 색과 어울리는 색으로 해준다. location : 10% 알파값 : 50

Width도 0.3정도 부터 시작하도록 설정

탄피 모델을 꺼내서 하이라키창에 넣어주기

탄피안의 Mesh Object의 Scale를 0.5로 맞춰주기

Rigidbody,  Box Collider 추가

Box Collider X: 0.45, Y: 0.3, Z: 0.3

Bullet 스크립트 만들기

스크립트 생성

### Bullet 스크립트

```c#
public class Bullet : MonoBehaviour
{
    public int damage;
    
    void OnCollisionEnter(Collision collision)
    {
        if(collision.gameObject.tag == "Floor")
        {
            Destroy(gmaeObject, 3);
        }
        else if(collision.gameObject.tag == "Wall")
        {
            Destroy(gameObject);
        }
    }
}
```

Bullet HandGun, Bullet SubMachineGun, Bullet Case에 스크립트를 넣어준다.

데미지를 설정해준다. (권총  20, 머신건 7)

총과 탄피를 프리펩으로 저장

위치를 0, 0, 0으로 초기화해줌

발사 구현 부터 보기