---
date: 2021-12-30
layout: post
title:  "C# Defensive Copy (readonly struct instance)"
description: "Defensive Copy"
image: /assets/img/posts/image/csharp.png
optimized_image: /assets/img/posts/image/csharp.png
category: C Sharp
tags:
  - C#
  - .NET
  - struct
  - readonly
author: chanos
---
>[Git Source](https://github.com/chanos-dev/blogcode/tree/master/21-1230){:target="_blank"}

---

안녕하세요. <b>chanos</b>입니다. 😉

오늘은 Defensive Copy에 대한 글입니다.

이 Defensive copy는 struct 타입이 readonly 예약어를 갖고 있는 인스턴스 일 때 발생할 수 있습니다.

코드에서 내부 필드 값을 바꾸려고 하는 경우 컴파일 오류가 발생하지만 struct 객체의 메서드를 통해 멤버 값을 수정하려는 경우, 이 때 defensive copy가 발생합니다.

다음과 같은 예제 코드를 살펴보도록 하겠습니다.

```c#
struct Foo
{
    public string Name;
    public int Age;

    public void IncAge()
    {
        this.Age++;
    }
}

class Program
{ 
    private readonly Foo ReadOnlyFoo = new Foo { Age = 30, Name = "홍길동" };
    private Foo NormalFoo = new Foo { Age = 24, Name = "심청이" };
    static void Main(string[] args)
    {
        var p = new Program();

        // p.NormalFoo.Age++;
        // p.ReadOnlyFoo.Age++; // Compiler Error CS1648 !!

        p.NormalFoo.IncAge(); 
        p.ReadOnlyFoo.IncAge(); 

        // 25
        Console.WriteLine(p.NormalFoo.Age);
        // 30
        Console.WriteLine(p.ReadOnlyFoo.Age);
    }
}
```

앞서 말씀드렸다시피 readonly 인스턴스의 멤버를 수정하려는 경우 컴파일 에러가 발생하지만 내부 메서드를 호출하는 경우는 에러가 발생하지 않습니다.

하지만 결과를 보면 readonly 인스턴스인 경우 증감 연산자가 적용되지 않은 것을 확인할 수 있습니다.

왜 그런건지 정성태님의 글을 인용하여 말씀드리면 c# 컴파일러에서 readonly 인스턴스가 value type이며 그 인스턴스의 메서드 또는 속성에 접근하려는 코드에 대해 내부적으로 임시 객체를 생성하여 임시 객체로 연산하도록 바꾼다고 합니다. 

ildasm.exe를 이용하여 IL을 확인한 결과는 다음과 같습니다.

![il](/assets/img/posts/2021-12-30/il.png)

굥교롭게도 위 코드를 분석할 능력은 아직 안되지만 정성태님의 힘을 빌려 해석하면 다음 코드로 재해석할 수 있습니다.

```c#
p.ReadOnly.IncAge();
-->
Foo temp = p;
temp.InceAge();
```

이 때문에 temp 객체에 값 복사가 일어나고 이 복사된 temp에 대해서 IncAge 메서드를 호출하기 때문에 기존 readonly 객체의 상태를 아무런 변화가 없게 됩니다.

이렇게 상태 변경을 방지하기 위해 내부적으로 수행되는 복사를 `Defensive copy`라고 칭합니다.

이 Definsive copy는 컴파일러에 의해 실행 때문에 코드 작성 시 예기치 않은 버그를 발생할 수 있어 주의가 필요할 것 같습니다.

---

## Reference

[C# - 값 형식의 readonly 인스턴스에 대한 메서드 호출 시 defensive copy 발생](https://www.sysnet.pe.kr/2/0/11523){:target="_blank"}