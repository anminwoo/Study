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



24b 10분 이후 부터 듣기