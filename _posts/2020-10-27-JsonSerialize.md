--- 
date: 2020-10-27
layout: post
title:  "C# Json Serialize, Deserialize"
description: "Json Serialize, Deserialize"
image: /assets/img/posts/image/csharp.png
optimized_image: /assets/img/posts/image/csharp.png
category: C Sharp
tags:
  - C#
  - .NET
  - json
author: chanos
---
>[Git Source](https://github.com/chanos-dev/blogcode/tree/master/20-1027)

--- 

- C#에서 Json을 간단하게 컨트롤할 수 있다. 👍
- JsonConvertor 클래스에 Serialize와 Deserialize를 사용하여 간단하게 Json을 직렬화, 역직렬화를 할 수 있고, Nuget에서 `Newtonsoft.Json`을 추가하여 사용할 수 있다.

![Json](/assets/img/posts/2020-10-27/Json.png)

>Person Class

```c#
class Person
{
    public Person() { }

    public string Name;
    public int Age;
    public string Sex;
    public string Address;
    
    public void PrintInfo(object obj, Person person)
    {
        if(obj is TextBox textBox)
        {
            textBox.Text = person.ToString();
        }
    }

    public override string ToString()
    {
        StringBuilder sb = new StringBuilder();
        sb.AppendLine($"Name : {this.Name}");
        sb.AppendLine($"Age : {this.Age}");
        sb.AppendLine($"Sex : {this.Sex}");
        sb.AppendLine($"Address : {this.Address}");

        return sb.ToString();
    }
}
```

>Person Class 직렬화

```c#
private void button_serialize_Click(object sender, EventArgs e)
{
    Person person = new Person();
    person.Name = "홍길동";
    person.Age = 23;
    person.Sex = "남자";
    person.Address = "경기도 광주시";

    string json = JsonConvert.SerializeObject(person);
    textBox1.Text = json;
}
```

>Person Class 역직렬화

```c#
private void button_deserialize_Click(object sender, EventArgs e)
{
    Person newPerson = JsonConvert.DeserializeObject<Person>(textBox1.Text);
    newPerson.PrintInfo(textBox2, newPerson);
}
```

- Json에는 `JsonArray` 형식도 가지고 있는데, C#에서 해당 Array형식을 클래스로 만들고 클래스 Array로 갖고있으면 `JsonArray`로도 만들 수 있다! 👏

![Json](/assets/img/posts/2020-10-27/JsonArr.png)

> Village Class, Fruit Class

```c#
class Village
{
    public Village() { }

    public string Location;
    public Fruit[] Fruit;

    public void PrintInfo(object obj, Village village)
    {
        if (obj is TextBox textBox)
        {
            textBox.Text = village.ToString();
        }
    }

    public override string ToString()
    {
        StringBuilder sb = new StringBuilder();
        sb.AppendLine($"Location : {this.Location}");

        foreach(Fruit fruits in Fruit)
        {
            sb.AppendLine($"Fruit.Name : {fruits.Name}");
            sb.AppendLine($"Fruit.Kcal : {fruits.Kcal}");
            sb.AppendLine($"Fruit.Price : {fruits.Price}");
        }            

        return sb.ToString();
    }
}

class Fruit
{
    public Fruit(string name, int kcal, int price)
    {
        Name = name;
        Kcal = kcal;
        Price = price;
    }

    public string Name;
    public int Kcal;
    public int Price;
}
```

> Village Class 직렬화

```c#
private void button_serializeArr_Click(object sender, EventArgs e)
{
    Village village = new Village();
    village.Location = "Korea";
    village.Fruit = new[] { new Fruit("Apple", 52, 1200),
                            new Fruit("Banana", 88, 800),
                            new Fruit("Strawberry", 32, 500)};

    string json = JsonConvert.SerializeObject(village);
    textBox1.Text = json;
}
```

> Village Class 역직렬화

```c#
private void button_deserializeArr_Click(object sender, EventArgs e)
{
    Village newVillage = JsonConvert.DeserializeObject<Village>(textBox1.Text);
    newVillage.PrintInfo(textBox2, newVillage);
} 
```

- 또한 클래스에서 Json에 역,직렬화를 원하지 필드가 있는 경우, 해당 필드에 `Attribute`를 선언하면 역,직렬화할 때 무시가된다! 😮

```c#
class Village
{
    public Village() { }

    public string Location;
    public Fruit[] Fruit;
    [JsonIgnore()] //해당 필드는 포함하지 않음!
    public int Count;
    ...
}
```