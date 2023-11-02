---
date: 2021-04-06
layout: post
title:  "C# 익명 타입"
description: "Anonymous Type"
image: /assets/img/posts/image/csharp.png
optimized_image: /assets/img/posts/image/csharp.png
category: C Sharp
tags:
  - C#
  - .NET
  - Anonymous Type
author: chanos
---
>[Git Source](https://github.com/chanos-dev/blogcode/tree/master/21-0406)

---

> <b> 익명 타입 </b> 😎

C# 3.0부터 클래스를 미리 정의하지 않고 사용할 수 있는 익명타입을 지원한다.

- 익명 타입 특징
    1. 읽기 전용이다. (할달 불가능)
    2. 변수할당 시 타입은 `var`
    3. new { 속성 : 값 } 형식으로 `Json` 형태와 비슷하다.
    4. 매개변수로 전달이 불가하고 리턴 타입으로도 사용할 수 없다.

사용방법)

![use1](/assets/img/posts/2021-04-06/use1.png)
- 컴파일러는 익명 타입에 대해 내부적으로 임의의 클래스를 생성한다.
- 이미지와 같이 setter가 없고 getter만 있다.

---

컴파일러는 이전에 만든 익명 타입을 재사용하지만 조건이 있다.
1. 동일한 어셈블리 내에 선언되어야한다.
2. 속성 이름과 타입이 일치해야 하며, 같은 순서로 선언되어야한다.

```c#
var person = new { Name = "홍길동", Age = 24, Address = "경기도 광주" };
var person2 = new { Name = "홍길동", Address = "경기도 광주", Age = 24 };
// 다음 두 인스턴스는 서로 다른 독립적인 익명타입을 생성한다.
```

---

익명 타입은 공식적으로 클래스를 정의할 필요 없이 Type을 간단히 임시로 만들어 사용할 때 유용하다. 특히 익명 타입은 LINQ를 사용할 때 많이 사용된다.

```c#
var cities = new[]
{
    new { Name = "Baek", Age = 50, Job = "Programmer"},
    new { Name = "Lee", Age = 20, Job = "Chef"},
    new { Name = "Park", Age = 30, Job = "Teacher"},
    new { Name = "Choi", Age = 40, Job = "Docter"},
};

var lists = cities.Where(c => c.Age <= 30).Select(c => new { c.Name, c.Job });

foreach(var list in lists)
    Console.WriteLine($"{list.Name} is {list.Job}");

// Select() 메서드를 사용하여 일부 컬럼들로만 구성된 새 익명 타입을 만들어 리턴한다.
```