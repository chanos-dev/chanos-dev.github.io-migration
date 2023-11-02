---
date: 2021-09-25
layout: post
title:  "C# MakeGenericMethod"
description: "MakeGenericMethod"
image: /assets/img/posts/image/csharp.png
optimized_image: /assets/img/posts/image/csharp.png
category: C Sharp
tags:
  - C#
  - .NET
  - Generic
author: chanos
---
>[Git Source](https://github.com/chanos-dev/blogcode/tree/master/21-0925){:target="_blank"}

---

제네릭을 사용하던 중 type parameter에 type 인스턴스를 넣어서 로직을 구현하고 싶어서 방법을 찾아보았다.

일반적으로 제네릭 타입에 type 인스턴스를 넣게되면 컴파일 에러[[CS0118](https://docs.microsoft.com/en-us/dotnet/csharp/misc/cs0118?f1url=%3FappId%3Droslyn%26k%3Dk(CS0118)){:target="_blank"}]가 발생한다. 

예를 들면 다음과 같은 상황이다.

```c#
internal enum FooType
{
    // values...
}

internal void GenericMethod<T>()
{
    // do something...
}

static void Main(string[] args)
{
    var p = new Program();

    var enumType = typeof(FooType);
    p.GenericMethod<enumType>(); // compile error!!
}
```

`p.GenericMethod<enumType>();` 구문과 같이 제네릭 type parameter에 type 인스턴스가 들어가는 경우 에러가 발생한다. 
- type parameter는 클래스명 또는 키워드와 같은 이름이 들어가야 한다.

type 인스턴스를 넣어서 사용하고 싶은 경우... 검색을 해본 결과 reflection을 이용해서 MakeGenericMethod를 사용하는 것이다.

사용법은 다음과 같다.

```c#
class Program
{
    internal enum FooType
    {
        // values...
    }

    public void GenericFoo<T>()
    {
        // do something...
    }

    public static void GenericBoo<T>()
    {
        // do something...
    }

    static void Main(string[] args)
    {
        var p = new Program(); 
        var enumType = typeof(FooType);
        // instance method
        p.GetType().GetMethod(nameof(Program.GenericFoo)).MakeGenericMethod(enumType).Invoke(p, null);

        // class method
        typeof(Program).GetMethod(nameof(Program.GenericBoo)).MakeGenericMethod(enumType).Invoke(null, null);        
    }
}
```

GetMethod를 이용하여 MethodInfo를 가져온 후 인스턴스 메서드인 MakeGenericMethod를 호출하여 제네릭 메서드로 만들어준다.

MakeGenericMethod의 인자로는 params type[]을 받으며 가져온 제네릭 메서드의 type paramater 인자 만큼 순서대로 인자로 넣어주면 된다.

마지막으로 MakeGenericMethod를 이용하여 생성된 MethodInfo에서 Invoke 인스턴스 메서드를 호출해주면 된다.

만약 제네릭 메서드가 클래스 메서드인 경우 Invoke 메서드의 첫번째 인자에 null을 넣어주면 되지만 인스턴스 메서드인 경우 호출하려는 메서드를 갖고있는 객체 인스턴스를 넣어줘야 한다.
- 해당 메서드의 파라메터가 있는 경우 두번째 인자에 object[] 형식으로 넣어주면 된다.)

---

## Reference

[MethodInfo.MakeGenericMethod(Type[]) 메서드](https://docs.microsoft.com/ko-kr/dotnet/api/system.reflection.methodinfo.makegenericmethod?view=net-5.0){:target="_blank"}