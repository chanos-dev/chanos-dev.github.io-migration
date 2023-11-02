---
date: 2021-04-08
layout: post
title:  "C# const, readonly"
description: "const, readonly"
image: /assets/img/posts/image/csharp.png
optimized_image: /assets/img/posts/image/csharp.png
category: C Sharp
tags:
  - C#
  - .NET
  - const
  - readonly
author: chanos
---
>[Git Source](https://github.com/chanos-dev/blogcode/tree/master/21-0408)

---

> <b> const, readonly </b> 🌈

const : 
- 한번 값이 할당되면 그 이후 변경이 불가능하다.
- static 변수이다.
- 클래스, 구조체 및 배열을 비롯한 사용자 정의 형식의 타입을 사용할 수 없다. (only primitive types, enums and string)
- 컴파일 타임에 처리된다.
- 컴파일 시 할당한 값이 어셈블리 메타데이터에 기록이 된다. (컴파일 상수)
- 배포 후 업데이트가 일어나면 참조하고 있는 어셈블리는 모두 리빌드를 해주어야한다.

readonly :
- 선언과 동시에 초기화를 하지 않아도 된다.
- 생성자에서 한번 더 할당이 가능하다.
- static 키워드와 사용되면 static 생성자에서만 할당이 가능하다.
- 모든 타입이 가능하다.
- 런타임에 처리된다.

```c#
private readonly Person person;// = new Person("홍길동", 24);
private static readonly Person person2 = new Person("심봉사", 24);
private const float Pi = 3.14f;

// constructor
public Program()
{
    person = new Person("홍길동", 24);
    // Pi = 3.1415f; (X)
    // person2 = null; ( X )
}

static Program()
{
    person2 = null;
}

class Person
{
    public string Name { get; set; }
    public int Age { get; set; } 

    public Person(string name, int age)
    {
        Name = name;
        Age = age;
    }
} 
```