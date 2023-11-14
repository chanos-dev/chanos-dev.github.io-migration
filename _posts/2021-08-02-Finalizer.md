---
date: 2021-08-02
layout: post
title:  "C# Finalizer"
description: "Finalizer"
image: /assets/img/posts/image/csharp.png
optimized_image: /assets/img/posts/image/csharp.png
category: C Sharp
tags:
  - C#
  - .NET
  - GC
  - Finalizer
author: chanos
---

>[Git Source](https://github.com/chanos-dev/blogcode/tree/master/21-0802){:target="_blank"}  
>[Memory Flow](https://github.com/chanos-dev/chanos-dev.github.io/tree/master/document/2021-08-02/Finalizer.drawio)

---

Finalizer
- c#의 메모리 관리는 CLR의 가비지 컬렉터로 관리가 되는데, 전부 그런 것은 아니다.
- 파일 IO, DB Connection 등 관리되지 않는 메모리 등을 사용하는 경우 이 자원들은 CLR에 의해 자동으로 메모리가 해제되지 않는다.
- 따라서 이러한 경우에는 개발자들이 직접 해제를 해주어야하는데, C++과 같이 Delete로 명시 해줄 수 없기 떄문에 CLR에서는 Finalizer라는 기법을 제공한다.

Finalizer 정의
- Finalizer는 생성자와 같이 클래스의 특수 메서드로 분류되는 메서드이다.
- 리턴 타입 및 파라미터 타입은 없으며 다음과 같이 정의한다. (`~` 키워드와 클래스명을 사용)
```c#
class Foo
{
    ~Foo()
    {

    }
}
```

Finalizer 호출 시점
- Finalizer 메서드는 GC에 의해 객체가 제거될 때 호출이되며 명시적으로 호출은 불가능하다.
- GC의 호출 주기는 알 수 없으므로, Finalizer 메서드가 호출되는 시점도 불명확하다.

GC - Finalizer 관계
- 객체를 Heap에 할당할 때, Finalizer 메서드를 갖고있는 객체라면 CLR은 Finalizer 메서드를 갖고있는 목록에 추가를 한다.
- GC가 수행될 때 Finalizer 메서드를 가지고 있는 객체가 정리 대상이라면 Freachable Queue 목록에 추가를 한다.
- Finalizer Thread라는 별도 스레드가 Freachable Queue 목록에 있는 객체들을 꺼내어 Finalizer 메서드를 호출한다.
- Finalizer 메서드가 호출이 완료된 객체는 다음 GC가 수행될 때 Heap에서 최종적으로 정리가 된다.
- 결국 Finalizer 메서드가 호출되기 전까지는 세대가 승급이 되면서 Heap상에 살아있는 것이다.
    - 계속 Heap에 남아있는 이유는 Frechable Queue에서 참조되어 있기 때문이다.

![Finalizer](/assets/img/posts/2021-08-02/Finalizer.jpg)

문제점
- Finalizer 메서드가 호출되기 전까지는 계속 Heap에 살아있기 때문에 메모리 효율이 떨어질 수 있다.
- Finalizer 메서드가 Finalizer Thread로 인해 호출되는 시점과 순서를 알 수 없다.
- Unmanaged Resources의 Finalizer 메서드가 중복으로 호출이 될 가능성이 있다.

중복 호출
```c#
class Foo
{
    private StreamWriter sw { get; set; }

    public Foo()
    {
        sw = new StreamWriter(@"C:\test.txt");
    }

    ~Foo()
    {
        sw?.Close();
    }    
}
```
```
처리되지 않은 예외: System.ObjectDisposedException: 닫혀 있는 파일에 액세스할 수 없습니다.
위치: System.IO.__Error.FileNotOpen()
위치: System.IO.FileStream.Flush(Boolean flushToDisk)
위치: System.IO.FileStream.Flush()
위치: System.IO.StreamWriter.Flush(Boolean flushStream, Boolean flushEncoder)
위치: System.IO.StreamWriter.Dispose(Boolean disposing)
위치: System.IO.StreamWriter.Close()
위치: _20_0802.Foo.Finalize()
```

- Foo 객체는 StreamWriter 객체를 속성으로 참조하고 있다. Foo와 StreamWriter 둘 다 Finalizer 메서드를 정의하고 있기 때문에 둘 다 Freachable Queue에 추가가 된다.

- Finalizer Thread로 인해 두 객체의 Finalizer 메서드가 호출이 될텐데, 만약 Thread가 StreamWriter 객체를 꺼내어 먼저 Finalizer 메서드를 호출했다면 StreamWriter 객체는 닫힐 것이다.

- 그 이후 Foo 객체를 꺼내어 Fianlizer 메서드를 호출한다면, 이미 닫힌 파일에 대해 또 다시 Close 메서드가 호출하여 ObjectDisposedException이 발생한 것이다.

Disposable 패턴
- 이러한 문제점을 해결하기 위해 제시된 방법이 Disposable 패턴이다. 
-  Disposable 패턴도 내용이 조금 있어 다음 포스팅 때 알아보도록 하겠다.

---

## Reference

[Finalizer 사용 시 주의 사항들](http://www.simpleisbest.net/post/2011/05/12/Finalizer-Usage-Pattern.aspx){:target="_blank"}