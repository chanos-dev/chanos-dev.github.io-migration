---
date: 2021-08-07
layout: post
title:  "C# IDisposable"
description: "IDisposable"
image: /assets/img/posts/image/csharp.png
optimized_image: /assets/img/posts/image/csharp.png
category: C Sharp
tags:
  - C#
  - .NET
  - GC
  - IDisposable
author: chanos
---
>[Git Source](https://github.com/chanos-dev/blogcode/tree/master/21-0804){:target="_blank"}

---

Finalizer를 선언하여 객체의 자원을 정리할 경우, 가비지 컬렉터의 메커니즘에 의해 객체의 제거 및 자원의 정리가 비결정적이라는 점을 해결하기 위해 제시된 방법이 IDisposable 패턴이다.

IDisposable 패턴이 구현된 객체는 using문을 이용할 수 있다.

using문을 이용하면 자동으로 해당 객체의 자원을 해제해주는 기능을 제공한다. (개발자에 의해 명시적 호출)
- using문을 이용하지 않은 경우 개발자가 직접 Dispose메서드를 호출해줘야한다.

```c#
// using문을 벗어나면 자동적으로 foo.Dispose() 메서드 호출
// using문에 선언되는 객체가 Dispose 메서드가 없는 경우 컴파일 에러가 난다.
using(var foo = new Foo())
{
    //do something.
}

// using을 사용하지 않은 경우
try
{
    var foo = new Foo();
    // do something.
}
finally
{
    foo?.Dispose();
}

```

IDisposable만 구현해준다면 Finalizer를 정의안해도 상관없을 것 같지만 그렇지 않다.

MSDN에서 제공되는 IDisposable 패턴의 표준 소스에도 Finalizer가 있는 것을 확인할 수 있다.

using문을 이용하여 개발자가 명시적으로 호출하는 경우 자원을 제때 정리할 수 있지만, using문 호출을 잊어버린 경우 CLR에서 호출할 수 있도록 Fianlizer를 정의하여(암시적 호출) 자원을 해제해줌으로 써 리소스 누수를 막을 수 있다.

그렇기에 귀찮더라도 Finalizer 메서드도 함께 정의한다.

> 구현

기본적으로 객체에 IDisposable 인터페이스를 상속받으면 인텔리센스에서 MSDN이 제공하는 표준 IDisposable 패턴을 자동으로 구현할 수 있다. (대박)

![idisposable](/assets/img/posts/2021-08-04/idisposable.png)

```c#
#region IDisposable Support
private bool disposedValue = false; // 중복 호출을 검색하려면

protected virtual void Dispose(bool disposing)
{
    if (!disposedValue)
    {
        if (disposing)
        {
            // TODO: 관리되는 상태(관리되는 개체)를 삭제합니다.
            // 
        }

        // TODO: 관리되지 않는 리소스(관리되지 않는 개체)를 해제하고 아래의 종료자를 재정의합니다.
        // TODO: 큰 필드를 null로 설정합니다.

        disposedValue = true;
    }
}

// TODO: 위의 Dispose(bool disposing)에 관리되지 않는 리소스를 해제하는 코드가 포함되어 있는 경우에만 종료자를 재정의합니다.
// ~Foo() {
//   // 이 코드를 변경하지 마세요. 위의 Dispose(bool disposing)에 정리 코드를 입력하세요.
//   Dispose(false);
// }

// 삭제 가능한 패턴을 올바르게 구현하기 위해 추가된 코드입니다.
public void Dispose()
{
    // 이 코드를 변경하지 마세요. 위의 Dispose(bool disposing)에 정리 코드를 입력하세요.
    Dispose(true);
    // TODO: 위의 종료자가 재정의된 경우 다음 코드 줄의 주석 처리를 제거합니다.
    // GC.SuppressFinalize(this);
}
#endregion
```

위 소스는 MSDN에서 제공하는 표준 IDisposable 패턴의 코드이다.

- protected virtual void Dispose(bool disposing)
    - 실제 자원들의 리소스 해제가 이루어지고 자식 클래스에서 지원 해제를 위해 정의된 메서드이다. (자식 클래스에서 재정의 후 base.Dispose를 호출 함으로 써 자식 클래스 및 부모 클래스 자원 해제)

- Finalizer
    - 개발자가 명시적으로 호출해주지 못한 경우 CLR에서 암시적으로 호줄해주기 위해 정의를 해준다.

- public void Dispose()
    - IDisposable 인터페이스에 정의된 메서드이다

- GC.SuppressFinalize(this)
    - Finalizer 메서드가 정의되어있으면 가비지 컬렉터가 Freachable queue에 쌓아 힙 메모리상에 더 오래 남아있게 되는데, GC.SuppressFinalize(this)를 호출하게되면 가비지 컬렉터에게 개발자가 직접 자원들을 해제 해줬으니 Freachable queue에 push를 안하겠다는 메서드이다.

인터페이스로 정의된 Dispose 메서드 호출 시 재정의된 Dispose 메서드에 true로 인자를 넘겨주고 있는데, 해당 인자로 인해 if (disposing) 조건을 타게된다. 이 조건은 관리형 리소스를 해제해주는 부분인데, 보통 byte[]와 같은 큰 객체의 참조를 끊어주기(GC의 루트 참조를 빨리 끊어버리기 위함) 위해 해당 필드(속성)에 null을 대입해주는 것이 좋다고 한다.

---

## Reference

[MSDN - IDisposable 인터페이스](https://docs.microsoft.com/ko-kr/dotnet/api/system.idisposable?view=net-5.0){:target="_blank"}

[Dispose 패턴](http://www.simpleisbest.net/post/2011/08/22/Dispose-Pattern-Basic.aspx){:target="_blank"}