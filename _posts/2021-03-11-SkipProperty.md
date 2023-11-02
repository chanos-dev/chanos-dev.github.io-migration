---
date: 2021-03-11
layout: post
title:  "C# GetProperties 특정 Property 제외하고 가져오기"
description: "Skip Property"
image: /assets/img/posts/image/csharp.png
optimized_image: /assets/img/posts/image/csharp.png
category: C Sharp
tags:
  - C#
  - .NET
  - Reflection
author: chanos
---
>[Git Source](https://github.com/chanos-dev/blogcode/tree/master/21-0311)

---

> <b> 특정 Property 제외 </b> 👓
 
- Reflection을 활용하여 특정 객체의 프로퍼티를 가져올 때 특정 프로퍼티는 제외하고 가져오고 싶을 때가 있다.

- 그런 경우 Attribute를 활용하여 특정 프로퍼티를 제외시킬 수 있다.

```c# 
class SkipPropertyAttribute : Attribute
{

}

class Person
{
	[SkipProperty]
	public string PersonalID { get; set; }
	public string Name { get; set; }
	public int Age { get; set; }
	public string Address { get; set; } 
}
```

- 다음과 같이 Attribute를 상속받아 사용자 정의를 하고, GetProperties시 제외시킬 프로퍼티에 적용시킨다.

```c#
//main
static void Main(string[] args)
{
	var properties = typeof(Person).GetProperties();

	foreach (var property in properties)
	{
		Console.WriteLine(property.Name);
	}

	Console.WriteLine("");

	var skipProperties = typeof(Person).GetProperties().Where(property => !Attribute.IsDefined(property, typeof(SkipPropertyAttribute)));

	foreach (var skip in skipProperties)
	{
		Console.WriteLine(skip.Name);
	}
}
```

- Attribute.IsDefined를 이용하여 SkipPropertyAttribute를 갖고있는 프로퍼티를 제외한 나머지를 갖고온다.

> 결과

![skipproperty](/assets/img/posts/2021-03-11/skipproperty.png)