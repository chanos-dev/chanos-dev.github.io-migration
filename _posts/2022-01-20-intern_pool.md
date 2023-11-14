---
date: 2022-01-20
layout: post
title:  "C# Intern Pool"
description: "Intern Pool"
image: /assets/img/posts/image/csharp.png
optimized_image: /assets/img/posts/image/csharp.png
category: C Sharp
tags:
  - C#
  - .NET
  - string
  - intern pool
author: chanos
---

>[Git Source](https://github.com/chanos-dev/blogcode/tree/master/22-0120){:target="_blank"}

>[Heap picture draw.io](https://github.com/chanos-dev/chanos-dev.github.io/tree/master/document/2022-01-20/heap.drawio){:target="_blank"}
                      
---

안녕하세요. <b>chanos</b>입니다. 😉

오늘은 Intern pool에 대한 글입니다.

Intern pool은 런타임 시 리터럴 문자열이 저장되는 특별한 공간입니다.

여기서 리터럴 문자열이란 소스코드 상에 쌍따옴표로 묶어 표기한 문자열을 칭합니다.

예를 들면 다음과 같죠.

```c#
var foo = "I love csharp!";
```

Intern pool에 등록하는 이유는 같은 리터럴 문자열이 사용된 경우 Intern pool에 등록된 같은 문자열의 주소를 참조하기 위함, 즉 메모리 관리 차원이 목적입니다.

```c#
var foo1 = "I love csharp~";
var foo2 = "I love csharp~";
var foo3 = "I love csharp~";

// foo1, foo2, foo3 모두 같은 주솟값을 참조
```

하지만 이러한 리터럴 문자열은 managed heap이 아닌 Intern pool이라는 특별한 공간에 할당되기 때문에 garbage collection(이하 GC)의 대상에서 벗어나게 됩니다.

즉, 프로그램이 종료될 때까지 메모리에 남아있다는 것을 뜻합니다.

---

이 Intern pool은 리터럴 문자열이 등록이 되는데 그렇다면 동적으로 생성된 문자열은 어떻게 될까요?

```c#
string s1 = "hello, world";
string s2 = string.Concat("hello, ", "world");
string s3 = string.Format("{0}, {1}", "hello", "world"); // 보간법도 동일합니다.
string s4 = new StringBuilder("hello, world").ToString();
string s5 = "hello, world";
 
Console.WriteLine($"equals : {s1.Equals(s2)} / reference equals {object.ReferenceEquals(s1, s2)}");
Console.WriteLine($"equals : {s1.Equals(s3)} / reference equals {object.ReferenceEquals(s1, s3)}");
Console.WriteLine($"equals : {s1.Equals(s4)} / reference equals {object.ReferenceEquals(s1, s4)}");
Console.WriteLine($"equals : {s1.Equals(s5)} / reference equals {object.ReferenceEquals(s1, s5)}");

// result
equals : True / reference equals : False
equals : True / reference equals : False
equals : True / reference equals : False
equals : True / reference equals : True
```

위 코드 결과를 보면 s5 변수 외에는 전부 reference equals의 리턴 값이 False로 떨어지게 됩니다.

왜 그런 것일까요?

그 이유는 동적으로 생성된 문자열들은 Intern pool에 등록되지 않고 managed heap에 할당되게 됩니다.

![heap](/assets/img/posts/2022-01-20/heap.jpg)

각 문자열에 대한 비교 equals는 전부 true로 나오지만 새롭게 할당된 문자열의 참조에 대한 비교는 false로 나온 것입니다.

---

그러면 Intern pool은 리터럴 문자열만 등록이 가능한가?

그렇지만은 않습니다. string 클래스 메서드를 살펴보면 다음과 같은 메서드를 찾아볼 수 있습니다.

```c#
public static String Intern(String str);
public static String IsInterned(String str);
```

위 메서드를 이용하여 Intern pool에 등록을 할 수 있고 다음은 각 메서드에 대한 설명입니다.

### Intern method
> 매개변수 str이 Intern pool에 존재하면 Intern pool에 등록된 str을 반환,  
그렇지 않다면 Intern pool에 등록 후 반환

### IsInterned method
> 매개변수 str이 Intern pool에 존재하면 Intern pool에 등록된 str을 반환,  
그렇지 않다면 null을 반환

- 사용 예제 코드

```c#
string s1 = "hello, world";
string s2 = string.Intern(string.Concat("hello, ", "world"));
string s3 = string.Concat("hello, ", "world");
string s4 = string.Format("{0}, {1}", "hello", "world");
string s5 = new StringBuilder("hello, world").ToString();
string s6 = string.IsInterned("Hello, csharp!");
 
Console.WriteLine($"equals : {s1.Equals(s2)} / reference equals {object.ReferenceEquals(s1, s2)}");
Console.WriteLine($"equals : {s1.Equals(s3)} / reference equals {object.ReferenceEquals(s1, s3)}");
Console.WriteLine($"equals : {s1.Equals(s4)} / reference equals {object.ReferenceEquals(s1, s4)}");
Console.WriteLine($"equals : {s1.Equals(s5)} / reference equals {object.ReferenceEquals(s1, s5)}");
Console.WriteLine($"equals : {s1.Equals(s6)} / reference equals {object.ReferenceEquals(s1, s6)}");

// result
equals : True / reference equals : True
equals : True / reference equals : False
equals : True / reference equals : False
equals : True / reference equals : True
equals : False / reference equals : False
```

변수 s2를 보면 string.intern method를 이용한 것을 확인할 수 있습니다.

s2는 string.concat으로 새로운 문자열을 만들어줬음에도 intern pool에 등록된 문자열을 가져오기 때문에 참조에 대한 비교 결과는 True로 나오게 됩니다.

또한 변수 s6는 현재 Intern pool에 등록되지 않은 문자열 즉 null을 반환하기 때문에 결과 값이 전부 False인 것도 확인이 가능합니다.

---

위에서 얘기했다시피 Intern pool은 GC 대상에서 벗어나게 됩니다.

GC 대상에서 벗어난다는 것을 확인하기 위해 WeakReference를 이용하여 확인해볼 수 있습니다.

```c#
string foo = "hello, world";
string weak = string.Concat("hello,", " world");
 
WeakReference w1 = new WeakReference(foo);
WeakReference w2 = new WeakReference(weak);
 
foo = null;
weak = null;
GC.Collect();
 
Console.WriteLine($"foo is alive ? {w1.IsAlive}");
Console.WriteLine($"weak is alive ? {w2.IsAlive}");
Console.WriteLine(string.IsInterned("hello, world"));

// result
foo is alive ? True
weak is alive ? False
hello, world
```

위 예제는 WeakReference를 이용하여 약한 참조로 연결을 해주고 실제 참조된 변수들은 전부 null 처리 후 GC를 강제 호출한 결과 입니다.

foo 변수는 Intern pool에 등록된 문자열이고 weak 변수는 동적으로 생성된, managed heap에 관리되는 문자열 입니다.

managed heap에 관리되는 weak는 정리된 반면 아직도 foo는 살아있는 것을 확인할 수 있습니다.
- 그 예로 string.IsInterned method에서 문자열을 반환하고 있습니다.

이렇게 Intern pool에 등록된 문자열은 GC의 대상에서 벗어나게 됩니다.

---

또한 간단하게 intern pool에 문자열을 등록했을 때와 그렇지 않았을 때의 string array에 string을 할당하여 GC에서 관리되고 있는 메모리를 확인하는 간단한 예제로 소개해 드리겠습니다.

```c#
static void CompareString(string foo)
{
    string[] strs = new string[10000];
 
    Console.WriteLine("Without intern");
    foreach (var i in Enumerable.Range(1, 10000))
    {
        strs[i-1] = $"{foo}!";
    }
    Console.WriteLine(GC.GetTotalMemory(true));
}
 
static void CompareStringWithIntern(string foo)
{
    string[] strs = new string[10000];
 
    Console.WriteLine("With intern");
    string.Intern($"{foo}!");
 
    foreach (var i in Enumerable.Range(1, 10000))
    {
        strs[i-1] = string.IsInterned($"{foo}!");
    }
    Console.WriteLine(GC.GetTotalMemory(true));
}

// result
Without intern
470748

With intern
71396
```

위 코드의 결과로 같은 문자열을 대입하고 있음에도 intern pool에 등록하지 않은 로직은 managed heap에 계속 할당되어 메모리가 증가한 것을 확인할 수 있습니다.

그렇다면 문자열을 Intern pool에 자주 등록하여 사용하면 좋지 않을까 싶지만 또 그렇지는 않습니다. 

위 예제에서도 `$"{foo}!"` 동적 문자열을 등록함으로써 프로그램이 종료될 때까지 메모리 영역에 남아있게 됩니다.

이 말은 빈번한 Intern pool 등록은 heap 메모리 영역을 계속 차지하게 되는 것이고 이로 인해 GC는 메모리 부족을 빨리 느껴 빈번하게 GC가 일어날 수 있게 됩니다. 

---
이렇게 Intern pool에 관련된 내용을 살펴보았습니다.

아직 필자는 이 Intern과 관련된 method들이 어디에 쓰일지 감이 잡히지는 않지만 리터럴 문자열이 관리되는 구조를 살펴보니 재미있는 시간이였습니다.

---

## Reference

[String.Intern(String) 메서드](https://docs.microsoft.com/ko-kr/dotnet/api/system.string.intern?view=net-6.0){:target="_blank"}

[String.IsInterned(String) 메서드](https://docs.microsoft.com/ko-kr/dotnet/api/system.string.isinterned?view=net-6.0){:target="_blank"}

[문자열 이야기 V : 인턴 풀(Intern Pool)이란?](http://www.simpleisbest.net/archive/2005/07/12/183.aspx){:target="_blank"} 

[C# String intern pool](https://www.decodejava.com/csharp-string-intern-pool.htm){:target="_blank"} 