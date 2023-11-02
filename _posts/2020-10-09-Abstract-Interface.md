--- 
date: 2020-10-09
layout: post
title:  "C# Abstract/Interface"
description: "Abstract/Interface"
image: /assets/img/posts/image/csharp.png
optimized_image: /assets/img/posts/image/csharp.png
category: C Sharp
tags:
  - C#
  - .NET
author: chanos
---
>[Git Source](https://github.com/chanos-dev/blogcode/tree/master/20-1009)

---

### - Abstract -
- 미완성된 클래스이다. (추상 메서드를 포함)
- 목적은 여러 개의 자식 클래스에 공통적인 정의를 제공하는 것이다. 
- 생성자를 가질 수 있다.
- 인스턴스화할 수 없다.
- 멤버 변수 선언이 가능하다.
- 접근 제한자를 사용할 수 있다. <b>(private, protected, internal)</b>
- 다중 상속이 불가능하다.

```c#
class Program
{
    static void Main(string[] args)
    {
        Animal[] animals = new Animal[3];
        animals[0] = new Dog("바둑이", "진돗개");
        animals[1] = new Cat("초롱이", "페르시안");
        animals[2] = new Pigeon("구구", "바위");  

        foreach (Animal animal in animals)
        {
            animal.GetInfo();
            animal.Sound();
            animal.Run();
            animal.Fly();
        }
        
    }
}

abstract class Animal
{       
    protected string Name;
    protected string Type; 
      
    public abstract void Run();
    public abstract void Fly();
    public abstract void Sound();

    public void GetInfo()
    {
        Console.WriteLine($"너의 이름은 {Name}, 종류는 {Type}!!");
    }  
}

class Dog : Animal
{
    public Dog(string name, string type)
    {
        Name = name;
        Type = type;
    }

    public override void Fly()
    {
        Console.WriteLine("멍멍~! 못날아요~!");
    }

    public override void Run()
    {
        Console.WriteLine("주인님 한테 달려가아~!");
    }

    public override void Sound()
    {
        Console.WriteLine("멍멍!");
    }


}

class Cat : Animal
{
    public Cat(string name, string type)
    {
        Name = name;
        Type = type;
    }

    public override void Fly()
    {
        Console.WriteLine("냐옹~! 못날아요~!");
    }

    public override void Run()
    {
        Console.WriteLine("상자 속으로 달려가아~!");
    }

    public override void Sound()
    {
        Console.WriteLine("냐옹!");
    }
}

class Pigeon : Animal
{
    public Pigeon(string name, string type)
    {
        Name = name;
        Type = type;
    }

    public override void Fly()
    {
        Console.WriteLine("하늘 위로..!!");
    }

    public override void Run()
    {
        Console.WriteLine("구구~! 못달려요~!");
    }

    public override void Sound()
    {
        Console.WriteLine("구구!");
    }
}
```
```
결과:
너의 이름은 바둑이, 종류는 진돗개!!
멍멍!
주인님 한테 달려가아~!
멍멍~! 못날아요~!
너의 이름은 초롱이, 종류는 페르시안!!
냐옹!
상자 속으로 달려가아~!
냐옹~! 못날아요~!
너의 이름은 구구, 종류는 바위!!
구구!
구구~! 못달려요~!
하늘 위로..!!
```
---

### - Interface -
- 멤버 변수를 선언이 불가능하다. (그 외 가능)
- 메서드의 구현이 없다. (선언만 가능)
- 모든 접근 제한자가 <b>public</b>이다.
- 여러 클래스의 공통적인 기능을 약속하기 위함이다.
- 다중 상속이 가능하다.

```c#
class Program
{
    static void Main(string[] args)
    { 
        Dragon dragon = new Dragon("용용이", "아이스드래곤");
        dragon.Breath();
        dragon.MoveFly();
    }
}

interface IAttack
{ 
    void Breath();
}

interface IMove
{
    void MoveGround();
    void MoveFly();
}

class Dragon : IAttack, IMove
{
    private string name;
    private string type; 

    public Dragon(string name, string type)
    {
        this.name = name; 
        this.type = type;
    } 

    public void Breath()
    {
        Console.WriteLine($"{this.name}의 공격!");
    }

    public void MoveFly()
    {
        Console.WriteLine($"{this.name}가 땅에서 이동");
    }

    public void MoveGround()
    {
        Console.WriteLine($"{this.name}가 하늘에서 이동");
    }
}
```
```
결과:
용용이의 공격!
용용이가 땅에서 이동
``` 