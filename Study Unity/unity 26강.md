UI에서 Image 선택

Anchor에서 화면 채우기 선택

배경이 될 이미지를 생성하고 자신이 원하는 색상과 투명도를 정해준다.

* (보통 게임들에서는 살짝 투명한 검은색을 사용함)

버튼을 감싸줄 이미지를 생성한다. 그 위에 버튼을 만들어준다.

<a style = "color : red">종료 버튼</a> 경우는 다른 버튼과 헷갈리지 않도록 색깔을 다르게 만들어준다.



Input 옵션에 Cancel 버튼이 있는지 확인

GameObject 기본 함수는 인스텍터 창에서 바로 할당 가능함

UI On / Off 기능

```c#
void Update()
{
	if (Input.GetButtonDown("Cancel"))
	{
		if (menuSet.ActiveSelf)
			menuSet.SetActive(false);
		else
			menuSet.SetActive(true);
	}
}
```



게임 종료 기능 (게임을 빌드해야 확인 가능함)

```c#
public void GameExit()
{
	Application.Quit();
}
```



게임 저장 기능

PlayerPrefs : 간단한 데이터 저장 기능을 지원하는 클래스

저장되는 곳은 기기마다 다름

Company, Product Name으로 레지스트리에 저장됨 <- 먼저 정하고 저장 기능   만드는 것이 좋음

```c#
public void GameSave()
{
   PlayerPrefes.SetFloat("PlayerX"player.transform.position.x);
   PlayerPrefes.SetFloat("PlayerY"player.transform.position.y);
    PlayerPrefes.SetFloat("QustId", questManger.questId); // 퀘스트 매니저에 있는 퀘스트 아이디
    PlayerPrefes.SetFloat("QustActionIndex",questManager.questActionIndex);
    PlayerPrefs.Save();
    
    menuSet.SetActive(false);
}
```



게임 불러오기 (저장되어 있던 데이터를 게임 오브젝트에 적용)

```c#
public void GameLoad()
{
    if(!PlayerPrefs.HasKey("PlayerX"))
        return;
	float x = PlayerPrefs.GetFloat("PlayerX");
    float y = PlayerPrefs.GetFloat("PlayerY");
    int questId = PlayerPrefs.GetInt("QustId");
    int questActionIndex = PlayerPrefs.GetInt("QustActionIndex");
    
    player.transform.position = new Vector3(x, y, 0);
    questManager.questId = questId;
    questManager.questActionIndex = questActionIndex;
    questManager.ControlObject();
}
```

불러오기 했을 당시의 퀘스트 순서와 연결된 오브젝트 관리를 추가해주어야함.