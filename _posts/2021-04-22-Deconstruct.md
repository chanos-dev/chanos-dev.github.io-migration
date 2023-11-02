---
date: 2021-04-22
layout: post
title:  "C# 튜플 사용자 정의 형식 분해"
description: "Deconstruct"
image: /assets/img/posts/image/csharp.png
optimized_image: /assets/img/posts/image/csharp.png
category: C Sharp
tags:
  - C#
  - .NET
  - Tuple
  - Deconstruct
author: chanos
---
>[Git Source](https://github.com/chanos-dev/blogcode/tree/master/21-0421)

--- 

C# 7.0 부터 Deconstruct라는 예약된 메서드를 제공한다. 대충 보면 소멸자(Destructor)와 스펠링이 비슷하게 생겨서 헷갈릴 수도 있다.

생성자(constructor)는 객체를 생성할 때 호출 되는 메서드로, 매개변수를 받아 인스턴스 멤버 변수를 만들고 초기화 하는데 사용을 하는데, Deconstruct는 그와 반대로 필드의 값들을 외부로 전달하는 역활을 한다.

다음은 Deconstruct 메서드를 구현하는 방법이다.
- 메서드 이름은 예약어인 Deconstruct를 사용한다.
- 리턴 값은 void를 반환해야 한다.
- 인스턴스 메서드여야 한다. (static X)
- 매개변수는 out 키워드를 붙인다.
- N개의 매개변수를 정할 수 있어 오버로딩이 가능하다.

```c#
class Person
{
    public string Name { get; set; }
    public int Age { get; set; }
    public string Job { get; set; }

    public Person()
    {

    } 

    // 지정된 예약어.. Deconstruct
    public void Deconstruct(out string Name)
    {
        Name = this.Name;
    }

    public void Deconstruct(out string Name, out int Age)
    {
        Name = this.Name;
        Age = this.Age; 
    }

    public void Deconstruct(out string Name, out int Age, out string Job)
    {
        Name = this.Name;
        Age = this.Age;
        Job = this.Job;
    }

    public override string ToString() => $"Name : {Name}\nAge : {Age}\nJob : {Job}";
}
```

- 사용 (Deconstruct 메서드의 파라메터 순서대로 var 변수에 할당하여 사용한다.)

```c#
static void Main(string[] args)
{
    var person = new Person()
    {
        Name = "백찬호",
        Age = 24,
        Job = "Programmer",
    };

    Console.WriteLine(person);

    var (name, age, job) = person;

    Console.WriteLine(name);
    Console.WriteLine(age);
    Console.WriteLine(job);

    var (name2, age2) = person;

    Console.WriteLine(name2);
    Console.WriteLine(age2); 
}
```

- 결과
![result](/assets/img/posts/2021-04-22/result.png)
---

## Reference

[튜플 사용자 정의 형식 분해](https://docs.microsoft.com/ko-kr/dotnet/csharp/deconstruct#deconstructing-user-defined-types){:target="_blank"}

[C# 7.0 Deconstructor](http://www.csharpstudy.com/Latest/CS7-deconstructor.aspx){:target="_blank"}