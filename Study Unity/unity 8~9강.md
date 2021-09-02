## unity 공부

**Time.deltaTime** 사용법

**Translate** : 벡터에 곱하기

transform.Translate(Vec * Time.deltaTime);



**Vector** 함수 : 시간 매개변수에 곱하기

Vector3.Lerp(Vec1, Vec2, T * Time.deltaTime);

deltaTime 값은 프레임이 적으면 크고, 프레임이 많으면 작음



|  RigidBody   | 중력의 영향을 받게 함                               |
| :----------: | --------------------------------------------------- |
|     Mass     | 수치가 높을수록 충돌이 무거워짐                     |
| Use Gravity  | 중력을 받는지를 결정함                              |
| Is Kinematic | 외부 물리효과 무시 (움직이는 함정을 만들 때 유용함) |

**Collider** : 물리효과를 받기 위한 컴포넌트

|  Material  | 오브젝트의 표면 재질을 결정하는 컴포넌트          |
| :--------: | ------------------------------------------------- |
|  Metalic   | 금속 재질 수치                                    |
| Smoothness | 빛 반사 정도                                      |
|  Texture   | 재질에 들어가는 이미지                            |
|   Tiling   | 텍스쳐 반복 타일 개수 (소수 넣으면 잘려서 들어감) |
|  Emission  | 텍스쳐 밝기 조절                                  |



|  Physics Material  | 탄성과 마찰을 다루는 물리적인 재질 |
| :----------------: | ---------------------------------- |
|     Bounciness     | 탄성력, 높을수록 많이 튀어오름     |
| Bounciness Combine | 다음 탄성을 계산하는 방식          |
|      Friction      | 마찰력, 낮을수록 많이 미끄러짐     |
|  Friction Combine  | 다음 마찰력을 계산하는 방식        |

