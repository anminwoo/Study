Random : 랜덤 수를 생성하는 로직 관련 클래스

Range() : 최소 ~ 최대 범위의 랜덤 수 생성 *(최대 제외)*



rigid.velocity = new Vector2(nextMove, rigid.velocity.y);
nextMove = Random.Range(-1, 2);



딜레이 타임을 주는 방법

Invoke() : 주어진 시간이 지난 뒤, 지정된 함수를 실행하는 함수

Invoke("함수 이름", 대기 시간);

CancelInvoke() : 현재 작동 중인 모든 Invoke함수를 멈추는 함수



적과 함정이 물리적인 충돌 방지하는 법

Project Settings로 들어가서 체크 해제



피해를 입었을 때 플레이어에게 알려주어야함.

spriteRenderer.color = new Color(R, G, B, A) *A가 투명도*



튕겨져 나가게 하는 법

int dirc = transform.position.x - targetPos.x > 0 ? 1: -1;

rigid.AddForce(new Vector2(dirc, 1) * 7, ForceMode2D.Impulse);

Invoke() 함수로 무적 시간을 결정함

Trigger : 방아쇠 역할의 매개변수, 값이 없다는 것이 특징

Any State -> Exit : 현재 상태 상관없이 실행 후 복귀

