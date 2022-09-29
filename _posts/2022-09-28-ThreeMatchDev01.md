---
title: ThreeMatch 개발기록 01
date: 2022-09-29 12:47:06+0900
categories: [Unity2D,ThreeMatch]
tags: [Atan2,Mathf,Mathf.Rad2Deg]
---

# **📐 두 위치값 사이 각도 구하기**

```cs
    private void CalculateAngle()
    {
        var swipeAngle = Mathf.Atan2(secondPos.y - firstPos.y, secondPos.x - firstPos.x) * Mathf.Rad2Deg;
        Debug.Log(swipeAngle);
    }

```
두 위치 값을 통해 |x2-x1| |y2 - y1| 거리 값을 구할 수 있으므로 탄젠트를 사용하여 두 위치값 사이 각도를 구할 수 있다.

## **📐 Mathf.Atan2**

탄젠트를 적용했을 때 지정된 두 숫자의 몫이 나오는 각도를 반환한다.

```cs
	public static float Atan2 (float y, float x);

```

반환 값
: Single

- -π ≤θ ≤π 범위의 각도 θ(라디안) 및 tan(θ) = y / x입니다(여기서 x, y는 데카르트 평면 상의 지점임). 다음 사항이 적용됩니다.

	- (x, y)가 1사분면 안에 있으면 0 < θ < π/2입니다. (0°~90°)

	- (x, y)가 2사분면 안에 있으면 π/2 < θ ≤ π입니다. (90°~180°)

	- (x, y)가 3사분면 안에 있으면 -π < θ < -π/2입니다. (0°~-90°)

	- (x, y)가 4사분면 안에 있으면 -π/2 < θ < 0입니다. (-90°~-180°)

- 점이 사분면의 경계에 있는 경우 반환 값은 다음과 같습니다.

	- y가 0이고 x가 음수가 아니면 θ = 0입니다.

	- y가 0이고 x가 음수이면 θ = π입니다.

	- y가 양수이고 x가 0이면 θ = π/2입니다.

	- y가 음수이고 x가 0이면 θ = -π/2입니다.

	- y가 0이고 x가 0이면 θ = 0입니다.

x 또는 y가 NaN이거나, x 및 y가 PositiveInfinity 또는 NegativeInfinity이면 이 메서드는 NaN을 반환합니다.

### 호도법과 라디안

![img-graphicspipeline](/assets/img/post/dev/radian.png)

호 AB 의 길이는 중심각에 비례한다. 따라서 부채꼴 호의 중심각 a는 반지름에 상관없이 항상 일정한 값을 가진다.

Π 라디안 = 180° 이며 1라디안은 180/Π , 1° = Π/180 라디안이다.

## **📐 Mathf.Rad2Deg**

라디안을 각도(degree/deg)로 변경하기 위한 상수로 360 / (PI * 2) 값을 가진다.


## 📌 Other & Reference

[호도법, 라디안](https://mathbang.net/496)

[MathF.Atan2(Single, Single) 메서드](https://learn.microsoft.com/ko-kr/dotnet/api/system.mathf.atan2?view=net-6.0) 
