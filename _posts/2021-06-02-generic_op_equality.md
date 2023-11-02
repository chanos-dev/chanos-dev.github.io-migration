---
date: 2021-06-02
layout: post
title:  "C# 제네릭메서드 안에서 객체 비교 문제"
description: "제네릭메서드 안에서 객체 비교 문제"
image: /assets/img/posts/image/csharp.png
optimized_image: /assets/img/posts/image/csharp.png
category: C Sharp
tags:
  - C#
  - .NET
  - op_Equality
author: chanos
---
>[Git Source](https://github.com/chanos-dev/blogcode/tree/master/21-0602)

--- 

- 저번 [문자열 비교]({{ site.url }}/string/)에서 제네릭 메서드 안에서 string 타입을 비교할 때 object ==연산인 ReferenceEquals연산을 호출하는 것 같다고 했었다.

- MSDN에서 문서를 확인해본 결과 제네릭 제약조건에 class를 주는 경우 ==, != 연산 사용시 참조 값이 같은지만 확인한다고 나와있다. (object.ReferenceEquals 사용)  
그래서 string 타입을 제네릭 메서드에서 == 연산을 하면 false, .Equals 연산을 하면 true가 나온 이유...😂  
Equals를 재정의한 경우(string 클래스는 재정의가 되어있으니) 제네릭에서 Equals 연산을 하면 재정의한 메서드를 호출하기 때문에 된 것..


- 서두가 조금 길었는데, 이 글의 요점은 제네릭 타입에서 참조가 아닌 값의 비교가 있을 때 제네릭 제약조건을 비교 interface가 정의된 객체들만 허용하게끔 개발자들에게 알려주는 것이다.  
e.g) `IEquatable<T>`, `IComparer<T>`, 등


- 다음과 같이 제네릭 메서드에서 값의 비교가 있는 경우 where 제약 조건에 IEquatable<T>를 추가하여 개발자들에게 IEquatable가 정의되어 있지 않은 객체는 사용할 수 없다는 에러메시지는 내주는 것이다.

```c#
static void Main(string[] args)
{
    var animals = new List<Animal>()
    {
        new Animal("Tiger", 5),
        new Animal("Lion", 8),
        new Animal("Tiger", 6),
        new Animal("Tiger", 4),
    };

    var tiger = new Animal("Tiger", 5);

    Console.WriteLine($"Same Animal count : {GetSameObejct(tiger, animals)}");

    var persons = new List<Person>()
    {
        new Person("Json", 5),
        new Person("Mika", 8),
        new Person("Mika", 6),
        new Person("Json", 4),
    };

    var person = persons[0];

    // 컴파일러 에러 : CS0311
    Console.WriteLine($"Same Animal count : {GetSameObejct(person, persons)}");
}

private static int GetSameObejct<T>(T target, List<T> animals)
    where T : IEquatable<T>
{            
    return animals.Where(animal => target.Equals(animal)).Count();
} 

class Animal : IEquatable<Animal> { ... }

class Person { ... }
```

---

## Reference

[형식 매개 변수에 대한 제약 조건](https://docs.microsoft.com/ko-kr/dotnet/csharp/programming-guide/generics/constraints-on-type-parameters){:target="_blank"}