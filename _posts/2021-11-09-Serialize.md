---
date: 2021-11-09
layout: post
title:  "C# Serialize"
description: "Serialize"
image: /assets/img/posts/image/csharp.png
optimized_image: /assets/img/posts/image/csharp.png
category: C Sharp
tags:
  - C#
  - .NET
  - Serialize
  - Deserialize
author: chanos
---
>[Git Source](https://github.com/chanos-dev/blogcode/tree/master/21-1108){:target="_blank"}

---

안녕하세요. <b>chanos</b>입니다. 😉

오늘은 데이터 직렬화(Serialize)와 역직렬화(Deserialize)에 대한 글입니다.

- 직렬화 :  객체를 파일 및 네트워크에 저장 / 전송 가능한 형태(스트림)로 만드는 작업

- 역직렬화 : 직렬화된 데이터를 객체의 형태로 변환하는 작업

### 데이터를 그냥 넘기면 되는게 아닌가? '왜' 직렬화 작업을 해야하는가?

저장 및 전송에 쓰이는 데이터의 형식은 값 형식(Value Type)입니다. 즉, Stack 영역에 할당된 데이터입니다.

하지만 객체의 실제 데이터는 Heap 메모리에 할당이되고 이를 참조하고 있는 주소 값이 Stack 영역에 할당되어 있습니다.

만약 직렬화를 하지 않는다면 객체의 실제 데이터가 아닌 Stack에 할당된 주소 값의 데이터를 사용하게 됩니다.

객체가 할당되는 주소 값은 컴퓨터마다 다르고 응용 프로그램에서 할당 됐던 주소 값을 저장했다가 재실행해서 해당 주소 값을 불러 오더라도 재실행되면서 메모리 영역이 해제되기 때문에 불러 올 수 없습니다.

그렇기에 이러한 주소 값은 무의미한 데이터가 됩니다.

이 때문에 Heap 영역에 있는 데이터를 Value Type으로 변환하여 저장 및 전송 가능하게 변환해주는 작업이 필요합니다.

---

### C# Serialize 사용

C#에서는 여러가지의 Serialize 포맷 방식이 있지만 이번 포스트에서는 BinaryFormatter를 이용합니다.

BinaryFormatter의 Serialize/Deserialize를 사용하기 위해선 객체에 `Serializable` Attribute를 선언해야합니다.

```c#
[Serializable]
class Foo
{
    public string Value { get; set; }
}
```

Foo라는 객체를 생성 후 BinaryFormatter를 이용하여 FileStream 또는 MemoryStream에 연속된 데이터를 만들 수 있습니다.

```c#
var foo new Foo()
{
    Value = "Hello, world!"
};

BinaryFormatter binartFormatter = new BinaryFormatter();

// Serialize
using (var stream = File.Open(path, FileMode.Create))
{
    binartFormatter.Serialize(stream, foo);
    Console.WriteLine(foo.Value);
} 

// Deserialize
using (var stream = File.Open(path, FileMode.Open))
{
    var deFoo = binaryFormatter.Deserialize(stream) as Foo;    
    Console.WriteLine(deFoo.Value);
}

// MemoeryStream 사용
using (var ms = new MemoryStream())
{
    binaryFormatter.Serialize(ms, foo);

    Console.WriteLine(Encoding.UTF8.GetString(ms.ToArray()));
}

```

### 결과
```
Hello, World
Hello, World
    ????          >21-1109, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null   _21_1109.Foo   <Value>k__BackingField      Hello, World
계속하려면 아무 키나 누르십시오 . . .
```

---

## Reference

[직렬화](https://ko.wikipedia.org/wiki/%EC%A7%81%EB%A0%AC%ED%99%94){:target="_blank"}

[BinaryFormatter](https://docs.microsoft.com/ko-kr/dotnet/api/system.runtime.serialization.formatters.binary.binaryformatter?view=net-5.0){:target="_blank"}