---
date: 2021-07-05
layout: post
title:  "C# IFormattable"
description: "IFormattable"
image: /assets/img/posts/image/csharp.png
optimized_image: /assets/img/posts/image/csharp.png
category: C Sharp
tags:
  - C#
  - .NET
  - IFormattable
author: chanos
---
>[Git Source](https://github.com/chanos-dev/blogcode/tree/master/21-0705)

---

사용자 지정 형식에는 (숫자, 문자) 등이 있는데 DateTime 타입을 예로 들면 다음과 같다.

```c#
static void Main(string[] args)
{
    Console.WriteLine($"{DateTime.Now}");
    Console.WriteLine($"{DateTime.Now:d}");
}

// Result
// 2021-07-05 오후 10:20:16
// 2021-07-05 
```

특정 키워드 (위 코드에서는 <b>`:d`</b> 이다.)를 넣어 해당 키워드에 맞게 정의된 문자열을 출력할 수 있다.

DateTime Struct에 가보면 IFormattable을 상속 받고 있는 것을 확인할 수 있다.

```c#
public struct DateTime : IComparable, IFormattable, IConvertible, ISerializable, IComparable<DateTime>, IEquatable<DateTime>
```

IFormattable을 사용하는 방법은 클래스에 IFormattable을 상속 받아 IFormattable에 정의된 ToString을 구현하면 된다.

```c#
// IFormatter Interface
public interface IFormattable
{ 
    string ToString(string format, IFormatProvider formatProvider);
}
```

- 구현해보기

```c#
class Calc : IFormattable
{       
    public int n1 { get; set; }
    public int n2 { get; set; }

    public Calc(int n1, int n2)
    {
        this.n1 = n1;
        this.n2 = n2;
    }

    public override string ToString() => ToString(string.Empty, CultureInfo.CurrentCulture);

    public string ToString(string format, IFormatProvider formatProvider)
    {
        if (string.IsNullOrEmpty(format))
            format = "None";

        if (formatProvider is null)
            formatProvider = CultureInfo.CurrentCulture;

        switch (format)
        {
            case "None":
                return $"n1 : {n1}, n2 : {n2}";
            case "+":
                return $"n1 + n2 : {n1 + n2}";
            case "-":
                return $"n1 - n2 : {n1 - n2}";
            case "*":
                return $"n1 * n2 : {n1 * n2}";
            case "/":
                return $"n1 / n2 : {n1 / n2}";
            default:
                throw new NotImplementedException();
        }
    }
}
```

IFormattable로 정의한 ToString메서드의 format 매개변수는 지정형식의 값이 들어오게 된다.
- e.g) $"{Foo:+}" -> format = "+"

object에 정의되어 있는 ToString을 override하여 IFormattable의 ToString을 호출하도록 하면 문자열 관리를 한 곳에서 할 수 있다.

위와 같이 ToString을 구현하면 다음과 같이 사용하면 된다.

```c#
var calc = new Calc(100, 50);

Console.WriteLine($"{calc}");
Console.WriteLine($"{calc:+}");
Console.WriteLine($"{calc:-}");
Console.WriteLine($"{calc:*}");
Console.WriteLine($"{calc:/}");

// result
n1 : 100, n2 : 50
n1 + n2 : 150
n1 - n2 : 50
n1 * n2 : 5000
n1 / n2 : 2

```

위 샘플 코드는 어떻게 사용하는지를 보여주기 위한 코드이니 실사용에는 상황에 맞게 적절하게 사용해보자. 😎

---

## Reference

[IFormattable](https://docs.microsoft.com/ko-kr/dotnet/api/system.iformattable?view=net-5.0){:target="_blank"}