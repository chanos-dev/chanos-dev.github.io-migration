---
date: 2021-06-07
layout: post
title:  "C# 원격 객체 생성"
description: "원격 객체 생성"
image: /assets/img/posts/image/csharp.png
optimized_image: /assets/img/posts/image/csharp.png
category: C Sharp
tags:
  - C#
  - .NET
  - Activator
  - Assembly.CreatInstance
  - FormatterServices
author: chanos
---
- C#에서 객체를 생성하는 방법에 대해서 알아보자. 여러가지 방법이 있지만 이번 포스트에서 소개할 방법은 Activator, Assembly,  FormatterServices클래스를 이용하여 생성하는 방법이다.

```
- Activator는 클래스 메서드인 CreateInstance를 사용한다.


- Assembly는 인스턴스 메서드인 CreateInstance를 사용한다.


- FormatterServices는 클래스 메서드인 GetUnititializedObject를 사용한다.
```

- FormatterServices 같은 경우는 새로운 객체를 메모리에 할당하지만 그 객체에 대하여 생성자를 호출하지 않는다. 그러나 그 객체의 모든 데이터는 null 또는 0으로 초기화가 된다.  
반면 Activator와 Assembly는 생성자가 있어야하고, 고로 파라미터를 넘겨 생성할 수 있다. public이 아닌 생성자는 BindingFlags를 이용해야한다.


- Assembly.CreateInstance를 디컴파일러로 까보면 결국엔 Activator를 호출하는 것을 확인할 수 있다.

![assembly](/assets/img/posts/2021-06-07/assembly.png)


- 위 방법으로 객체를 생성하는 방법은, 런타임에서 객체의 타입 또는 객체 이름을 스트링 타입으로 가지고만 있어도 생성이 가능하다는 점이다.

- 그렇다면 Activator와 Assembly를 이용하여 객체를 생성할 때 차이점이 존재하는데, Activator는 다른 어셈블리를 로드시켜 모두 리플렉션을 할 수 있고(객체 Type 또는 AssemName만 안다면), Assembly는 현재 로드한(도메인, 어쎔블리) 내에서만(Assembly의 CreateInstance 인스턴스 메서드를 호출하기 전에 Assembly를 로드한다.) 리플렉션을 할 수 있다는 것이다.


- 사용

```c#
// No parameter
// 1. Activator
var person_instance1 = Activator.CreateInstance(typeof(Person));
Console.WriteLine(person_instance1);
 
// 2. Assembly
var assem = Assembly.GetExecutingAssembly();
var typeName = assem.DefinedTypes.Where(val => val.Name == nameof(Person)).FirstOrDefault().FullName;
var person_instance2 = assem.CreateInstance(typeName, true);
Console.WriteLine(person_instance2);
 
// 3. FormaterServices 
var person_instance3 = FormatterServices.GetUninitializedObject(typeof(Person));
Console.WriteLine(person_instance3);
 
// Parameter
var parameter1 = new object[] { "홍길동", 30 };
var parameter2 = new object[] { "고길동", 35 };
 
var person_instance4 = Activator.CreateInstance(typeof(Person), BindingFlags.Public | BindingFlags.Instance, null, parameter1, CultureInfo.CurrentCulture, null);
Console.WriteLine(person_instance4);
 
var person_instance5 = assem.CreateInstance(typeName, true, BindingFlags.Public | BindingFlags.Instance, null, parameter2, CultureInfo.CurrentCulture, null);
Console.WriteLine(person_instance5);
 
// class
class Person
{
    public string Name { get; set; }
    public int Age { get; set; }
 
    public Person(string name, int age)
    {
        Name = name;
        Age = age;
    }
 
    public Person() : this("admin", 999)
    {
 
    }
 
    public override string ToString() => $"Name : {Name}, Age : {Age}";
}  
```

- 결과
![result](/assets/img/posts/2021-06-07/result.png)
---

## Reference

[Activator 클래스 (System)](https://docs.microsoft.com/ko-kr/dotnet/api/system.reflection.assembly.createinstance?view=netcore-3.1){:target="_blank"}

[FormatterServices 클래스 (System.Runtime.Serialization)](https://docs.microsoft.com/ko-kr/dotnet/api/system.runtime.serialization.formatterservices?view=netcore-3.1){:target="_blank"}

[Assembly.CreateInstance 메서드 (System.Reflection)](https://docs.microsoft.com/ko-kr/dotnet/api/system.activator?view=netcore-3.1){:target="_blank"}