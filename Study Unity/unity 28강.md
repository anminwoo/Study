슈팅 게임 총알 발사 구현 하는 법

충돌 이벤트를 위한 콜라이더와 리지드바디 생성

리지드바디의 Gravity Scale 0으로 설정

프리펩 : 재활용을 위해 에셋으로 저장된 게임 오브젝트

Destroy() : 매개변수 오브젝트를 삭제하는 함수

총알 제거 경계를 생성하여 화면 밖으로 넘어간 총알 프리펩을 삭제함

Instantiate() : 매개변수 오브젝트를 생성하는 함수

총알프리펩에  Is Trigger 옵션 켜기

if문으로 **특정 버튼**을 눌렀을 때 발사하게 할 것인지 아니면 **특정 버튼**이 눌리지 않았을 때 return 으로 반환하게 할 것인지

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
    }
    
    void Reload()
    {
        curShotDelay += Time.deltaTime;
        
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

