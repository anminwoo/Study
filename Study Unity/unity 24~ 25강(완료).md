퀘스트 데이터를 저장할 변수를 생성

구조체 선언

```c#
using System.Collections;
using System.Collections.Generic;

public class QuestData
{
	public string questName;
	public int[] npcId;
	
	public QuestData(string name, int[] npc)
}
```



```c#
public class QestManager : MonoBehaviour
{
	questList = new Dictionary<int, QuestData>();//데이터 저장
	GenerateData();
}

void GenerateData()
{
	questList.Add(10, new QuestData("퀘스트 이름                                                 , new int[{}npc아이디"))
	
}

public int GetQuestTalkIndex(int id) // NPC Id를 받고 퀘스트   번호를 반환하는 함수
{
    return questId;
}
```



퀘스트 대화순서 변수를 사용하여 

무시되는 npc가 없도록 만들어야한다. (혹은 예외 경우에 나올 말을 정해줘야함)

퀘스트번호, 퀘스트 대화순서에 따라 오브젝트 조절 (특정 아이템)

ContainsKey() : Dictionary에 Key가 존재하는지 검사

Overloading : 매개변수에 따라 함수 호출

#### 초상화 이펙트

대화창의 얼굴 표정이 바뀌는 것을 표현할 때

전에 있던 초상화와 현재 초상화를 비교해서 다르다면 애니메이션 실행



#### 글자 이펙트

글자 재생 속도를 위한 변수 생성 : CharPerSeconds (CPS)

```c#
public class TypeEffect : MonoBehaviour
{
	public int CharPerSeconds; // CPS
    public GameObject EndCursor // 대화창 마지막에 나오는 화살표 모양
	string targetMsg; // 대화 문자열을 받는 함수
	Text msgText; // Text 변수 생성, 초기화, 시작함수에서 공백 처리
    int index;
    
    private void Awake()
    {
       msgText = GetComponent<Text>();
	}
	public void SetMsg(string msg)
	{
        if(isAnim) // 애니메이션이 진행중이라면
        {
            msgText.text = targetMsg; // 텍스트 띄우기
            CancleInvoke(); // Invoke 취소
            EffectEnd(); // 이펙트 끝네기
		}
        else
        {
            targetMsg = msg;
            EffectStart();
        }
	}
	
	void EffectStart() // 시작
    {
        msgText.text = "";
        index = 0;
        EndCursor.SetActive(false); // 화살표 비활성화
        
        //Start Animation
        interval = 1.0f / CharPerSeconds; // 확실한 소수값을 얻기 위해 분자 1.0f 작성
        Debug.Log(interval);
        
        isAnim = true
        Invoke("Effecting", 1/CharPerSeconds); // 1초 / CPS = 1글자가 나오는 딜레이
	}
    
    void Effecting() // 재생
    {
        if(msgText.text == targetMsg) // 대화 문자열과 Text 내용이 일치하면 종료
        {
            EffectEnd();
            return;
		}
        msgText.text += targetMsg[index] // 문자열도 배열처럼
          char 값에 접근이 가능함.
        index++;
        
        Invoke("Effecting", 1/CharPerSeconds);
    }
    
    void EffectEnd() // 종료
    {
        isAnim = false;
    	EndCursor.SetActive(true); // 화살표 활성화
	}
}
```



매니저에서도 플래그 변수를 이용하여 분기점 로직을 작성해야함

