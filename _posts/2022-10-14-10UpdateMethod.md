---
title: chapter 10 업데이트 메서드
date: 2022-10-22 21:56:06+0900
categories: [GameProgramming,GameProgrammingPattern]
tags: [designPattern,updateMethod]
---
# **👀 업데이트 메서드 (Update Method)**
    
## 🤔패턴 의도

컬렉션에 들어 있는 객체별로 한 프레임 단위의 작업을 진행하라고 알려 전체를 시뮬레이션한다.

## 🧐 업데이트 메서드는 왜 사용되는가?

게임에서 플레이어가 보물을 찾으러 이동할 때 지키던 해골 경비병이 캐릭터에게 다가가거나 왔다갔다하며 순찰해야할 것 이다.

이런 움직임을 한프레임에 한번에 진행하면 갑자기 캐릭터가 순간이동하게 된다. 한프레임에 한 걸음 씩 움직이도록 해야 캐릭터가 움직이는 것처럼 보일 것 이다.

## 👩‍💻예제 코드

게임 루프 내에서 해골 병사가 좌우로 왔다 갔다하는 코드를 만들어보면 아래와 같다.

```cpp
Entity skeleton;
bool patrollingLeft = false; //이동 방향 변수
double x = 0;

// 게임의 메인 루프
while (true)
{
  if (patrollingLeft)
  {
    x--;
    if (x == 0) patrollingLeft = false;
  }
  else
  {
    x++;
    if (x == 100) patrollingLeft = true;
  }

  skeleton.setX(x);

  // 유저 입력 처리와 게임 렌더링을 진행한다...
}
```
이와 같이 메인루프에 해골병사의 이동 코드가 들어가게되면서 메인 루프에 여러 코드가 추가되었다. 해골 병사가 다른 기능이 추가되거나 다른 여러 몬스터들이 추가되면 각 개체에 대한 변수, 로직등으로 메인루프가 복잡해지게 된다.

당연히 이 문제의 해결책은 **모든 개체가 자신의 동작을 캡슐화**하여 해결할 수 있다.

이를 위해 추상 메서드 update()를 정의해 추상 계층을 더한다. 게임 루프는 업데이트가 가능하다는 것만을 아는 객체 컬렉션을 관리하며 게임 엔진과 다른 객체를 분리한다.

## 🧩 업데이트 메서드 패턴

이제 게임 월드는 **객체 컬렉션**을 관리한다. 각 객체는 **한 프레임 단위의 동작을 시뮬레이션하기 위한 업데이트 메서드를 구현**한다. 매 프레임마다 게임은 컬랙션에 들어 있는 **모든 객체를 업데이트**한다.

## ⏰ 언제 사용되는가?

- 동시에 동작해야 하는 객체나 시스템이 게임에 많다.
- 각 객체의동작은 다른 객체와 거의 독립적이다.
- 객체는 시간의 흐름에 따라 시뮬레이션되어야 한다.

## 🔍사용 시 주의 사항

### 🚩 코드를 한 프레임 단위로 끊어서 실행하는 게 더 복잡하다.

동작 코드를 프레임마다 실행되도록 쪼개 넣으면 코드가 복잡해져서 구현 비용이 좀 더 발생한다.

### 🚩다음 프레임에서 다시 시작할 수 있도록 현재 상태를 저장해야 한다.
	
이와 같이 프레임이 진행되면서 변화된 상태에 대한 저장은 상태 패턴을 사용하는 것이 좋다.
### 🚩모든 객체는 매 프레임 마다 시뮬레이션되지만 진짜로 동시에 되는 건 아니다.

객체 컬렉션 내에서 업데이트가 순차적으로 이뤄지므로 순서가 중요해진다.

객체 목록에서 A가 B보다 앞에 있다면 A는 B의 이전 프레임 상태를 참조하게 된다. B의 차례에는 A는 이미 업데이트된 상태이므로 A의 현재 프레임 상태를 보게된다. 

### 🚩업데이트 도중에 객체 목록을 바꾸는 건 조심해야 한다.

게임이 진행되다보면 객체를 게임에 추가 및 삭제하는 코드도 포함하게 된다.

**객체 추가의 경우**

객체가 새로 생긴다면 객체 목록 뒤에 추가하면 목록을 순회하며 마지막에 새로 생긴 객체까지 업데이트하게된다.

다만,이때 새로 생성된 객체가 바로 작동되므로 이를 방지하고 싶다면 업데이트 루프를 시작하기 전에 목록에 있는 객체 개수를 미리 저장해 두고 그만큼만 업데이트를 진행하면 된다.

```cpp
int numObjectsThisTurn = numObjects_;
for (int i = 0; i < numObjectsThisTurn; i++)
{
  objects_[i]->update();
}
```
**객체 삭제의 경우**

![img-graphicspipeline](/assets/img/post/programmingPattern/updateMethodRemove.png)

객체가 삭제되었다면 객체 목록에서 빼내야한다. 하지만 삭제해버린다면 의도치 않게 객체를 하나 건너뛸 수 있다.

이를 고려하여 객체를 삭제할때 순회 변수 i를 업데이트하는 방법도 있다. 또는 목록을 다 순회할 때까지 삭제를 늦추거나 업데이트 도중 죽은 객체의 변수를 죽음으로 표시하고 넘어갈 수 도 있다.

## 예제 코드 

아래는 해골 병사,석상등등을 표현할 객체 클래스이다.

```cpp
class Entity
{
public:
  Entity()
  : x_(0), y_(0)
  {}

  virtual ~Entity() {}
  virtual void update() = 0; // 추상 메서드 update

  double x() const { return x_; }
  double y() const { return y_; }

  void setX(double x) { x_ = x; }
  void setY(double y) { y_ = y; }

private:
  double x_;
  double y_;
};
```

이제 게임의 월드 클래스에서 개체 컬렉션 관리가 진행된다.
```cpp
{
public:
  World()
  : numEntities_(0)
  {}

  void gameLoop();

private:
  Entity* entities_[MAX_ENTITIES];
  int numEntities_;
};
```
게임 루프는 아래와 같이 돌아간다.
```cpp
void World::gameLoop()
{
  while (true)
  {
    // Handle user input...

    // 각 개체의 업데이트를 호출한다.
    for (int i = 0; i < numEntities_; i++)
    {
      entities_[i]->update();
    }

    // Physics and rendering...
  }
}
```
개체를 상속받는 스켈레톤에 대한 구현은 아래와 같다. 상태와 로직이 메인 루프와 분리되어 구현된다.
```cpp
class Skeleton : public Entity
{
public:
  Skeleton()
  : patrollingLeft_(false)
  {}

  virtual void update()
  {
    if (patrollingLeft_)
    {
      setX(x() - 1);
      if (x() == 0) patrollingLeft_ = false;
    }
    else
    {
      setX(x() + 1);
      if (x() == 100) patrollingLeft_ = true;
    }
  }

private:
  bool patrollingLeft_;
};
```
이와 같이 업데이트 메소드 패턴을 사용하면 객체가 자신이 필요한 모든 걸 직접 들고 관리 할 수 있디 때문에 게임 월드에 새로운 개체를 추가하기가 쉬워진다.

![img-graphicspipeline](/assets/img/post/programmingPattern/UpdateMethodUML.png)

## 🤔업데이트 메서드는 어느 클래스에 둬야할까?

### 🚩개체 클래스

가장 간편한 방법이나 새로운 동작을 만들때 마다 개체 클래스를 상속받아야 한다면 작업이 어렵다. 단일 상속 구조로 코드를 매끄럽게 재사용할 수 없는 순간에 방법이 없다.

### 🚩컴포넌트 클래스

컴포넌트는 알아서 자기 자신을 업데이트하며 컴포넌트 패턴은 한 개체의 일부를 개체의 다른 부분들과 디커플링하여 렌더링,물리,AI 모두 스스로 돌아가도록 한다.

### 🚩위임 클래스

상태 패턴, 타입 객체 패턴등 하나를 쓴다면 위임 클래스에 update()를 두는 게 자연스럽다. 여전히 update() 메서드는 개체 클래스에 있지만 가상 함수가 아니며 위임 객체에 포워딩을 해준다.

```cpp
void Entity::update()
{
  // Forward to state object.
  state_->update();
}
```

새로운 동작을 정의하고자 한다며 위임 객체를 바꾸기만 하면 된다.
컴포넌트 방식과 마찬가지로 완전히 새로운 상속 클래스를 정의하지 않아도 동작을 바꿀 수 있는 유연성을 얻는다.

## 🪄휴먼 객체 처리

사용 불능 상태이거나 화면 밖에 있는 등의 이유로 업데이트가 필요없는 객체가 생길 수 있다. 이런 객체가 많다면 매 프레임마다 쓸데없이 CPU 클럭 낭비가 생길 수 가 있다.

이를 방지하는 방법으로 활성 객체만 모여 있는 컬렉션을 하나 더 둘 수 도 있다. 다만 이경우엔 두 번째 컬렉션을 위해 **메모리를 추가로 사용**해야한다는 점과 **컬랙션 두 개의 동기화를 유지**해야 한다는 점을 고려해야한다.

## 📌 Other & Reference

[gameprogrammingpatterns.com/update-method](https://gameprogrammingpatterns.com/update-method.html)

**더 알아보기**

[마이크로소프트 XNA 플랫폼 Game 클래스](https://learn.microsoft.com/en-us/previous-versions/windows/xna/bb197040(v=xnagamestudio.42))

