---
date: 2021-03-10
layout: post
title:  "C# 객체 비교"
description: "IEquatable"
image: /assets/img/posts/image/csharp.png
optimized_image: /assets/img/posts/image/csharp.png
category: C Sharp
tags:
  - C#
  - .NET
  - IEquatable
author: chanos
---
>[Git Source](https://github.com/chanos-dev/blogcode/tree/master/21-0310)

---

> <b> 객채 비교 </b> 🎰
 
- 객체의 참조값이 아닌 속성 값을 통해 비교하고 싶을 때 사용할 수 있는 패턴으로 [C# 형식에 대한 값 같음 프로그래밍 가이드](https://docs.microsoft.com/ko-kr/dotnet/csharp/programming-guide/statements-expressions-operators/how-to-define-value-equality-for-a-type)에서 제공하는 방식이다.  

- IEquatable<T>를 상속받아 Equlas<T> 정의 및 Object virtual method인 Equlas와 GetHashCode를 override하고 마지막으로 operator ==, operator!=를 정의한다.

- 다음은 EqualsPerson 클래스에서 PersonalID가 같으면 같음을 비교한다.

```c#
class EqualsPerson : IEquatable<EqualsPerson>
{
	public string PersonalID { get; set; }
	public string Name { get; set; }
	public int Age { get; set; }              

	public override bool Equals(object obj)
	{
		return this.Equals(obj as EqualsPerson);
	}

	public bool Equals(EqualsPerson other)
	{
		if (other is null)
			return false;

		if (Object.ReferenceEquals(this, other))
			return true;

		return this.PersonalID == other.PersonalID;
	}

	public static bool operator ==(EqualsPerson left, EqualsPerson right)
	{
		if (left is null)
		{
			if (right is null) 
				return true; 

			return false;
		}

		return left.Equals(right);
	}

	public static bool operator !=(EqualsPerson left, EqualsPerson right) => !(left == right);

	public override int GetHashCode()
	{
		var hashCode = 1345254698;
		hashCode = hashCode * -1521134295 + EqualityComparer<string>.Default.GetHashCode(PersonalID);
		hashCode = hashCode * -1521134295 + EqualityComparer<string>.Default.GetHashCode(Name);
		hashCode = hashCode * -1521134295 + Age.GetHashCode();
		return hashCode;
	}

	public override string ToString() => $"PersonalID : {PersonalID}, Name : {Name}, Age : {Age}";
}
```

> Main

```c#
// NormalPerson은 IEquatable이 구현되지 않은 클래스이다.
static void Main(string[] args)
{  
	var person1 = new NormalPerson()
	{
		PersonalID = "1",
		Name = "홍길동",
		Age = 24,
	};

	var person2 = new NormalPerson()
	{
		PersonalID = "1",
		Name = "이몽룡",
		Age = 25,
	}; 

	var person3 = person1;

	Console.WriteLine("=== NormalPerson ===");
	Console.WriteLine($"person1 : {person1.ToString()}");
	Console.WriteLine($"person2 : {person2.ToString()}");
	Console.WriteLine($"person3 : {person3.ToString()}");
	Console.WriteLine($"person1 == person 2 : {person1.Equals(person2)}");
	Console.WriteLine($"person1 == person 3 : {person1.Equals(person3)}");
	Console.WriteLine(""); 

	var ePerson1 = new EqualsPerson()
	{
		PersonalID = "1",
		Name = "홍길동",
		Age = 24,
	};

	var ePerson2 = new EqualsPerson()
	{
		PersonalID = "1",
		Name = "이몽룡",
		Age = 25,
	};

	var ePerson3 = ePerson1;

	Console.WriteLine("=== EqualsPerson ===");
	Console.WriteLine($"ePerson1 : {ePerson1.ToString()}");
	Console.WriteLine($"ePerson2 : {ePerson2.ToString()}");
	Console.WriteLine($"ePerson3 : {ePerson3.ToString()}");
	Console.WriteLine($"ePerson1 == ePerson 2 : {ePerson1.Equals(ePerson2)}");
	Console.WriteLine($"ePerson1 == ePerson 3 : {ePerson1.Equals(ePerson3)}"); 
}
```

> 결과

![equals](/assets/img/posts/2021-03-10/equals.png)