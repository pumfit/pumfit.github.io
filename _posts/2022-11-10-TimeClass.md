---
title: "[Unity] UnityEngine.Time Time.time"
date: 2022-11-10 21:00:06+0900
categories: [Unity,UnityEngine]
tags: [time,timeclass]
---
# ⏰유니티 Time.time 정리
Time.time을 이용하여 한 씬의 플레이 시간을 체크하고자 했다. 하지만 생각대로 씬 전환이 되면 Time.time이 초기화되는 것이 아니라는 것을 알게되어 정리를 하게 되었다.
    
## ⏱️UnityEngine.Time

유니티에서 제공하는 시간 정보를 가져오기 위한 인터페이스이다.

## ⌚Time.time
Time.time은 게임 시작 이후의 시간이다. 이 시간은 해당되는 프레임의 시작 시간으로 한 프레임 내에서 여러번 호출된다면 동일하게 해당 프레임의 시작 시간이 호출된다.

FixedUpdate에서 이 Time.time을 호출한다면 fixedTime과 동일한 값을 반환한다.

Awake에서는 시간 값은 정의되지 않으며, Awake가 완료된 이후부터 시간이 흐른다.

### 🔍 씬 내에서의 진행 시간 구하는 방법

**1. Time.deltaTime을 사용하기**
```csharp
private Update()
{
	playTime += Time.deltaTime;
}
```
**2. Time.time을 사용하기**
```csharp
private Start()
{
	/*Reset StartTime*/
	startTime = Time.time;
}

private Update()
{
	playTime = Time.time - startTime;
}
```

## 📌 Other & Reference

https://docs.unity3d.com/ScriptReference/Time-time.html
