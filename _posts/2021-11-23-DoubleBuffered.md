---
date: 2021-11-23
layout: post
title:  "C# DoubleBuffered"
description: "DoubleBuffered"
image: /assets/img/posts/image/csharp.png
optimized_image: /assets/img/posts/image/csharp.png
category: C Sharp
tags:
  - C#
  - .NET  
  - Winforms
  - DoubleBuffered
author: chanos
---

>[Drawio](https://github.com/chanos-dev/chanos-dev.github.io/tree/master/document/2021-11-24/DoubleBuffered.drawio)

---

안녕하세요. <b>chanos</b>입니다. 😉

오늘은 C# Winform에서 `DoubleBuffered`에 대한 글입니다.

우리는 Winform에서 가끔 컨트롤이 깜빡이는 현상을 발견할 수 있습니다.

윈도우 폼에서 컨트롤이 화면출력을 위해 다시 그려질 때 발생하는 문제인데 Control 객체 속성 중 `DoubleBuffered` 속성을 이용하면 손쉽게 해결할 수 있습니다.

하지만 `DoubleBuffered` 속성은 protected 접근 제어자를 갖고 있기 때문에 Control을 상속받고 있는 Form 디자이너 객체에서는 바로 접근이 가능합니다.

일반적인 Panel과 같은 컨트롤 같은 경우에는 와부에서 접근이 불가능하기 떄문에 특정한 방법으로 접근해야 합니다.

현재 알고있는 방법은 총 2가지인데 이 방법들을 설명하기 전에 `DoubleBuffered`가 무엇인지 확인하고 가보겠습니다.

`DoubleBuffered`는 화면에 그려줄 출력 버퍼를 2개를 만들어 화면을 그리게 됩니다.

A 버퍼는 화면을 출력하는 동안 B 버퍼는 내부적으로 화면에 출력될 화면을 그립니다. B 버퍼의 화면이 최종적으로 완료가 되면 A 버퍼에 전송하여 사용자는 화면을 다시 출력하는 중간 과정을 보지않고 바로 최종 결과를 볼 수 있게 됩니다.

![DoubleBuffered](/assets/img/posts/2021-11-23/DoubleBuffered.png)

위 과정을 통해 사용자는 최종결과만을 보기 때문에 깜빡이는 현상을 해결할 수 있게 됩니다.

그럼 C#에서 DoubleBuffered를 사용하는 방법을 알아보도록 하겠습니다. 

위에서 언급했다시피 DoubleBuffered는 protected 속성이기 때문에 Control을 상속 받는 방법과 Reflection의 GetProperty을 이용하는 방법이 있습니다.

### Control 상속
```c#
/// <summary>
/// DoubleBuffered가 필요한 컨트롤을 상속받고 대입  
/// 디자이너 .cs 파일로 이동하여 객체를 바꿔주어야 한다.
/// </summary>
internal class DoubleBufferedListView : ListView
{
    public DoubleBufferedListView()
    {
        this.DoubleBuffered = true;
    }
}
```

### Reflection 이용
```c#
/// <summary>
/// Reflection을 이용하여 대입
/// </summary>
internal static class ControlExtension
{
    internal static void DoubleBuffered(this Control control, bool isBuffered)
    {
        var prop = control.GetType().GetProperty("DoubleBuffered", BindingFlags.Instance | BindingFlags.NonPublic); 
        prop.SetValue(control, isBuffered);

        // 위 로직 또는 아래 로직 사용.

        control.GetType().InvokeMember("DoubleBuffered", BindingFlags.Instance | BindingFlags.NonPublic | BindingFlags.SetProperty, null, control, new object[] { isBuffered });
    }
}
```

---

## Reference

[더블 버퍼링](https://chaejiho.tistory.com/2){:target="_blank"}

[[C#] 더블버퍼링이란?](https://junwe99.tistory.com/32){:target="_blank"}