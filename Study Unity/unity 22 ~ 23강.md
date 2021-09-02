대화창 UI 구현하기

Slice 옵션을 사용하려면 Sprite 설정이 필요함

Anchor를 사용하여 원하는 곳에 대화창이 있을 수 있도록 설정한다.

판넬 이미지 안에 Text 생성

텍스트 2줄 넣어보면서 높이와 폰트크기를 조정한다.

데이터 전달을 위한 매니저 생성

UnityEngine.UI 넣고 만들기

```c#
// 대화창
using UnityEngine.UI;

public class GameManger : MonoBehaviour
{
    public Text talkText;
    public GameObject scanObject;
    
    public ovid Action(GameObject scanObj)
    {
        scanObject = scanObj;
        talkText.text = "이것의 이름은 " + scanObject.name + "이라고 한다.";
    }
}
```

플레이어에서 **매니저 함수**를 호출할 수 있게 변수를 생성해야함

인스펙터 창에서 public변수 채우기

SetActive() 함수로 숨기기 & 보여주기를 구현

```c#
// 이건 2D 게임에서 상호작용 중 이동하는 것을 제한한다.
bool hDown = manager.isAction ? false : Input.GetButtonDown("Horizontal");

bool hDown = manager.isAction ? false : Input.GetButtonDown("Vertical");

bool hDown = manager.isAction ? false : Input.GetButtonDown("Horizontal");

bool hDown = manager.isAction ? false : Input.GetButtonDown("Horizontal");
```

UI 안에 애니메이션을 넣어 계속하기 모양을 만들수 있다.

Set Native Size : 원본 크기대로 이미지 사이즈를 맞춤

Anchor 적용된 UI는 Anchored Position 선택해야한다.

Y값만 변경하면 아래 혹은 위로 올라가도록 설정이 가능하다.

반복되는 애니메이션은 파일을 클릭 -> Lope Time을 체크해야한다.



 id로 대화 Get -> talkIndex로 한 문장을 Get

초상화를 보여주려면 Image UI가 필요함

Image UI 접근을 위해 변수 생성 & 할당

초상화 스프라이트를 저장할 배열 생성

Split() : 구분자를 통하여 배열로 나눠주는 문자열 함수

Parse() : 문자열을 해당 타입으로 변환해주는 함수

Parse()는 문자열 내용이 타입과 맞지 않으면 오류가 발생함

