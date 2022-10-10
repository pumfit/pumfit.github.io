---
title: chapter 8 이중 버퍼 패턴
date: 2022-10-07 21:00:06+0900
categories: [GameProgramming,GameProgrammingPattern]
tags: [designPattern,doublebuffer]
---

# **👀 이중 버퍼 (Double Buffer)**

이중 버퍼는 여러 순차 작업의 결과를 한번에 보여주기 위해 사용된다.
    
## 이중버퍼의 필요성
- **티어링(tearing,화면 밀림)현상**

![img-graphicspipeline](/assets/img/post/programmingPattern/tearing.jpg)

게임이 실행되는 동안 비디오 디스플레이가 프레임버퍼를 반복해서 읽는데 **프레임버퍼에 값이 쓰이는 와중에 비디오 드라이버가 프레임 버퍼 값을 읽어 값을 쓰는 도중 읽게 되면** 화면이 깨지게되는 것이다.

![img-graphicspipeline](/assets/img/post/programmingPattern/doubleBuffer.png)

이때, 이중 버퍼 패턴을 통해 코드에서는 픽셀을 한번에 하나씩 그리되, 비디오 드라이버는 전체 픽셀을 한번에 다 읽을 수 있게 한다.

프레임버퍼를 두개 준비한다. 하나의 프레임버퍼는 보이는 프레임저퍼로 값은 해당 버퍼에서만 읽을 수 있다. 그동안 렌더링 코드는 다른 프레임버퍼에 값을 쓰고 모든 값을 쓴 뒤 **버퍼를 교체**한다.

## ⏰언제 이중 버퍼를 사용하는가?

- 순차적으로 변경해야 하는 상태가 있다.
- 이 상태는 값 변경 도중에도 접근 가능해야한다.
- 바깥 코드에서는 작업 중인 상태에 접근할 수 없어야 한다.
- 싱태에 값을 쓰고 있는 도중에도 대기없이 바로 접근이 가능해야한다.

## 🧐구현시 주의 사항
1. **교체 연산 자체에 시간이 걸린다.**

	값을 모두 입력한 경우 버퍼를 교체하게 되는데 이 교체 연산은 원자적이어야 한다. 즉, 교체가 진행될 때 두 버퍼 모두에 접근 할 수 없어야 한다는 점을 생각해야한다.
	
3. **버퍼가 두 개 필요하다.**

	메모리 버퍼가 두 개가 필요하므로 메모리 부족한 상황에서는 이중 버퍼 패턴이 부담이 될 수 있다.
	
## 👩‍💻예제 코드
아래는 단순 그래픽 시스템 예제이다.
```cpp
class Framebuffer // 프레임 버퍼 클래스
{
public:
  Framebuffer() { clear(); }

  void clear() // 모든 버퍼를 흰색으로 초기화한다.
  {
    for (int i = 0; i < WIDTH * HEIGHT; i++)
    {
      pixels_[i] = WHITE;
    }
  }

  void draw(int x, int y)// 특정 픽셀을 검은 색으로 칠한다.
  {
    pixels_[(WIDTH * y) + x] = BLACK;
  }

  const char* getPixels()//픽셀 배열을 불러온다.
  {
    return pixels_;
  }

private:
  static const int WIDTH = 160;
  static const int HEIGHT = 120;

  char pixels_[WIDTH * HEIGHT];
};
```
`getPixels()` 메서드를 통해 픽셀 데이터를 담고 있는 메모리 배열에 접근하며 비디오 드라이버가 화면을 그리기 위해 버퍼값을 해당 함수를 통해 호출한다.

```cpp
class Scene
{
public:
  void draw()
  {
    buffer_.clear();
	// 씬에서 버퍼의 draw함수를 통해 순차적으로 그림을 그려나간다.
    buffer_.draw(1, 1);
    buffer_.draw(4, 1);
    buffer_.draw(1, 3);
    // 이 중간에 비디오 드라이버가 getPixels을 호출하면 문제가 생기게 된다.
    buffer_.draw(2, 4);
    buffer_.draw(3, 4);
    buffer_.draw(4, 3);
  }

  Framebuffer& getBuffer() { return buffer_; }

private:
  Framebuffer buffer_;
};
```
여러번 `draw()`를 호출해 그림을 그리는 것을 볼 수 있다. 이때, 드라이버에서는 언제나 `getPixels()`을 통해 버퍼에 접근할 수 있다. 

아래는 이중 버퍼를 통해 문제를 해결하는 방법이다.
```cpp
class Scene
{
public:
  Scene() // 버퍼 포인터 값을 초기화한다.
  : current_(&buffers_[0]),
    next_(&buffers_[1])
  {}

  void draw()
  {
    next_->clear();

    next_->draw(1, 1);
    // ...
    next_->draw(4, 3);

    swap();// 모든 정보를 쓴 뒤 next버퍼를 current버퍼로 교체한다.
  }

//읽는 버퍼는 current_를 반환한다.
  Framebuffer& getBuffer() { return *current_; }

private:
  void swap()
  {
    // 버퍼 포인터만 교체한다.
    Framebuffer* temp = current_;
    current_ = next_;
    next_ = temp;
  }

  Framebuffer  buffers_[2]; // 버퍼를 포인터가 current,next 두가지 버퍼를 가리킨다.
  Framebuffer* current_; // 읽기가 가능한 버퍼
  Framebuffer* next_; // 쓰기가 가능한 버퍼
};
```
이제 렌더링할 때는 next_ 포인터가 가리키는 다음  버퍼에 쓰고 그리고, 비디오 드라이버는 current_ 포인터로 현재 버퍼에 접근해 픽셀을 읽어온다.

이렇게 이중 버퍼를 사용하면 `getBuffer()`가 호출되면 **이전에 그린 화면이 들어있는 current 버퍼**를 얻게 된다.

## 🔍유니티에서의 이중 버퍼 활용


### 커스텀 렌더 텍스처

[**커스텀 렌더 텍스처 유니티 도큐먼트** ](https://docs.unity3d.com/kr/2019.4/Manual/class-CustomRenderTexture.html)

커스텀 렌더 텍스처는 셰이더를 사용하여 텍스처에 직접 렌더링할 수 있는 렌더 텍스처의 확장이다.

유니티에서 커스텀렌더텍스처를 사용할 때`CustomRenderTexture.doubleBuffered` 값을 설정하여 업데이트 도중에도 접근가능하게 설정할 수 있다.

|프로퍼티  |기능  |
|--|--|
| **Double Buffered** |  텍스처가 이중 버퍼링됩니다. 각 업데이트는 두 개의 버퍼를 스왑하여 사용자가 셰이더에서 이전 업데이트의 결과를 읽을 수 있도록 합니다.|


## 📌 Other & Reference

[game-programming-patterns github - double-buffer](https://github.com/munificent/game-programming-patterns/blob/master/code/cpp/double-buffer.h)

[Tearing Image](https://commons.wikimedia.org/wiki/File:Tearing_(simulated).jpg#/media/File:Tearing_(simulated).jpg)

[doubleBuffer Image](https://gameprogrammingpatterns.com/double-buffer.html)

**👾더 알아보기**

[MFC 이중 버퍼 예제 관련 포스팅](https://adnoctum.tistory.com/149)

[OpenGL에서 사용되는 swapbuffers](https://learn.microsoft.com/ko-kr/windows/win32/api/wingdi/nf-wingdi-swapbuffers)
