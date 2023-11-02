---
date: 2021-06-09
layout: post
title:  "C# 캡처 변수"
description: "캡처 변수"
image: /assets/img/posts/image/csharp.png
optimized_image: /assets/img/posts/image/csharp.png
category: C Sharp
tags:
  - C#
  - .NET
  - Closure
  - Captured Variable
author: chanos
---
>[Git Source](https://github.com/chanos-dev/blogcode/tree/master/21-0609)

--- 

- 람다식 등을 구현할 때 로컬변수를 사용하는 경우, 이를 클로저(Closure)라 부르는데 로컬변수를 사용하는 경우 캡처 변수라는 것을 주의해야한다. ⚠

- 다음 코드를 살펴보면 개발자가 기대한 값이 0, 1, 2, 3, 4 이지만 실제 실행하여 결과를 확인하면 5, 5, 5, 5, 5가 출력되는 것을 확인할 수 있다.

```c#
static void Main(string[] args)
{
    var actions = new List<Action>();

    for (int idx = 0; idx < 5; idx++)
    { 
        actions.Add(() => Console.WriteLine(idx));
    }

    foreach (var action in actions)
    {
        action.Invoke();
    }
}
```

- 이를 해결해주기 위해서는 for문 scope안에 새로운 로컬변수를 할당하여 대리자에 넘겨주어야 한다. 이를 반영하여 수정한 코드는 다음과 같을 것이다.

```c#
static void Main(string[] args)
{
    var actions = new List<Action>();

    for (int idx = 0; idx < 5; idx++)
    { 
        int temp = idx;
        actions.Add(() => Console.WriteLine(temp));
    }

    foreach (var action in actions)
    {
        action.Invoke();
    }
}
```

- 위 코드를 실행하면 정상적으로 기대한 값이 출력되는 것을 확인할 수 있다.

- 🤔 얼핏 보기엔 로컬 변수 idx가 값 복사가 이루어져 대리자에 넘어가는 것이 아닌가 싶지만, 클로저를 사용하게되면 동작방식이 다르다.

- Reference에 있는 article을 살펴보면, 람다식에 사용되는 변수는 컴파일러로 인해 따로 익명의 private 클래스의 멤버 변수로 옮겨지고 람다식의 내용도 그 클래스의 메서드로 옮겨지게 된다.

- 이에 따라 처음에 작성했던 코드는 컴파일러에 의해 다음과 같이 변경이 된다.

```c#
class ComplierClass
{
    public int _i;
 
    public void _method()
    {
        Console.WriteLine(_i);
    }
}
 
static void Main(string[] args)
{  
    var actions = new List<Action>();
 
    ComplierClass complier = new ComplierClass();
 
    for (int complier._i = 0; complier._i < 5; complier._i++)
    {  
        actions.Add(complier._method);
    }
 
    foreach(var act in actions)
    {
        act.Invoke();
    }
}
```

- for문 scope 밖에서 임시객체가 생성이되고, for문 안에서 임시객체의 _i 맴버에 계속 대입을 해주니 기대했던 값과 다르게 나온 것이다.

- 그럼 수정된 코드는 컴파일러에 의해 어떻게 변경이 되었는지 다음 코드를 살펴보자

```c#
class ComplierClass
{
    public int _temp;
 
    public void _method()
    {
        Console.WriteLine(_temp);
    }
}
 
static void Main(string[] args)
{  
    var actions = new List<Action>(); 
 
    for (int idx = 0; idx < 5; idx++)
    {  
        ComplierClass complier = new ComplierClass();
        complier._temp = i;
        actions.Add(complier._method);
    }
 
    foreach(var act in actions)
    {
        act.Invoke();
    }
}
```

- 위 코드는 임시객체가 for문 scope안에서 생기는걸 확인할 수 있다. loop마다 새로이 객체가 생성되고 새로운 객체의 멤버에 대입을 한번만 해주니 기대했던 결과를 얻어낼 수 있는 것이다.

---

## Reference

[Codeproject Inside C# 2.0 Anonymous Methods](https://www.codeproject.com/Articles/15624/Inside-C-2-0-Anonymous-Methods#4){:target="_blank"}

[.NET Framework: 523. C# 람다(Lambda)에서 변수 캡쳐 방식](https://www.sysnet.pe.kr/2/0/10817){:target="_blank"}