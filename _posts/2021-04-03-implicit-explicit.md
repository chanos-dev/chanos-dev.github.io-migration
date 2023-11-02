---
date: 2021-04-03
layout: post
title:  "C# 사용자 정의 형변환"
description: "implicit explicit"
image: /assets/img/posts/image/csharp.png
optimized_image: /assets/img/posts/image/csharp.png
category: C Sharp
tags:
  - C#
  - .NET
  - implicit
  - explicit
author: chanos
---
>[Git Source](https://github.com/chanos-dev/blogcode/tree/master/21-0403)

---

> <b> 사용자 정의 형변환 </b> 🎈

implicit, explicit 두 가지 모두 C# 4.0 부터 제공하는 연산자이다.

---

implicit : 별도의 캐스팅 없이 컴파일러가 알아서 변환하는 것, 반환에 의한 값 손실이 없을 때 사용한다.

```c#
class Time
{
    public int Hour { get; set; }

    public int Minute { get; set; }

    public int Second { get; set; }

    public static implicit operator int(Time time) => time.Hour * 3600 + time.Minute * 60 + time.Second;
}
```

```c#
static void Main(string[] args)
{
    var time = new Time()
    {
        Hour = 5,
        Minute = 30,
        Second = 15,
    };

    int totalSec = time;

    Console.WriteLine($"Sec : {totalSec}");
}
```
- int totalSec = time 과 같이 별도의 캐스팅 없이도 대입이 가능하다.

---

explicit : 값 손실이 발생할 수 있어, 캐스팅에 의해서만 반환이 가능하다.

```c#
class Meter
{
    public float M { get; set; }

    public Meter(float m) => M = m;

    public static implicit operator string(Meter m) => $"{m.M}m";

    public static explicit operator Centimeter(Meter m) => new Centimeter(m.M * 100);
}

class Centimeter
{
    public float Cm { get; set; }

    public Centimeter(float cm) => Cm = cm;

    public static implicit operator string(Centimeter c) => $"{c.Cm}cm";

    public static explicit operator Meter(Centimeter c) => new Meter(c.Cm / 100);
}
```

```c#
static void Main(string[] args)
{
    var meter = new Meter(5.5f);

    Console.WriteLine(meter);

    var centimeter = (Centimeter)meter;

    Console.WriteLine(centimeter);  
}
```
- var centimeter = (Centimeter)meter와 같이 클래스가 다르더라도 explicit을 이용하면 클래스 간 캐스팅이 가능하다.


---

![result](/assets/img/posts/2021-04-03/result.png)