아이템 만들기

매쉬가 포함된 오브젝트의 위치, 각도 조절하기

새 자식 오브젝트를 만들고 Light 컴포넌트를 추가

|                               |                                                              |
| ----------------------------- | ------------------------------------------------------------ |
| Type                          | 광원타입(여기서는 point 사용)                                |
| Intensity                     | 빛의 세기                                                    |
| Range                         | 빛의 범위                                                    |
| Color                         | 빛의 색상                                                    |
| Emission                      | 파티클 입자 출력양                                           |
| Shape                         | 파티클 입자 출력 모양                                        |
| Color Over Life Time          | 시간에 따른 색상변화 (중간 부분을 누르면 중단점이 생기고 거기에서 그라데이션을 넣을 수 있음.) |
| Size Over Life Time           | 시간에 따른 크기변화                                         |
| Limit Velocity over Life Time | 시간에 따른 속도 제한 (Drag는 저항값)                        |
| Start Life Time               | 입자 생존 시간 (Random Between Two Constants로 랜덤한 모양 가능) |
| Start Speed                   | 입자 시작 속도                                               |
|                               |                                                              |
|                               |                                                              |
|                               |                                                              |



Particle System 컴포넌트를 추가

파티클 효과의 색상이 <a style = "color: Magenta">자홍색(Magenta)</a>이라면 재질의 못찾았다는 의미이다.

Renderer에서 Material을 찾아서 연결 (여기서는 Default-Line을 사용함)



로직 구현 부터

