---
date: 2021-01-04
layout: post
title:  "C# 확장메서드"
description: "Extension Method"
image: /assets/img/posts/image/csharp.png
optimized_image: /assets/img/posts/image/csharp.png
category: C Sharp
tags:
  - C#
  - .NET
  - Extension Method
author: chanos
---
>[Git Source](https://github.com/chanos-dev/blogcode/tree/master/21-0104)

---

> <b>확장 메서드란?</b> 🔨

- 어떠한 클래스에 메서드를 추가하고 싶을 때 클래스를 수정하거나 파생 클래스를 만들지 않고 추가하는 방법이다.
- 확장메서드는 `static` 클래스 및 메서드로 정의되어야하고, 확장 메서드의 첫 번째 파라미터는 확장 메서드가 확장하려고하는 클래스의 식별자로 정의하며 `this` 예약어를 붙여야한다.
- 두 번째 파라미터가 없을 경우는 생략이 가능하다.
- `static` 메서드이지만 사용은 인스턴스 메서드처럼 사용된다.
- 확장 메서드의 대표적으로 `LINQ`가 있다.

> <b>확장 메서드 사용</b> ⌨

```c#
static class ExtensionClass
{
    public static int Factorial(this int i)
    {
        if (i < 2)
            return 1;
        else
            return i * Factorial(i - 1);
    }

    public static string StringConcat(this string str, string str2)
    {
        return str + str2;
    }
}
class Program
{
    static void Main(string[] args)
    {
        int inumber;
        Console.Write("Factorial : ");
        if (int.TryParse(Console.ReadLine(), out inumber))
            Console.WriteLine(inumber.Factorial().ToString());
        else
            Console.WriteLine("Input Error"); 

        string str = "Hello"; 
        Console.WriteLine(str.StringConcat(" World"));
    }
}
```

```
결과:
Factorial : 6
720
Hello World
계속하려면 아무 키나 누르십시오 . . .
```

> <b> IntelliSense </b> 🔖

- 확장 메서드를 만들게 되면 다음과 같이 박스형태와 화살표로 이루어져 있다.

![extension](/assets/img/posts/2021-01-04/extension.png)

- LINQ (Select, Where 등)

![linq](/assets/img/posts/2021-01-04/linq.png)