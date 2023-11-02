---
date: 2021-12-24
layout: post
title:  "C# IMessageFIlter"
description: "IMessageFIlter"
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
>[Git Source](https://github.com/chanos-dev/blogcode/tree/master/21-1224){:target="_blank"}

---

안녕하세요. <b>chanos</b>입니다. 😉

오늘은 C#의 IMessageFilter Interface에 대한 글입니다.

Application에서 Windows Message를 처리해야할 때 사용할 수 있는 것이 IMessageFilter 입니다.

IMessageFilter는 PreFilterMessage를 구현해야하며 매개변수로는 struct 타입으로 Windows Message를 받습니다.

리턴 타입으로는 bool 타입이 사용되는데 이 값은 MessageFilter에서 처리한 Message를 Application에 보낼건지 대한 값입니다.

- Windows Message를 Filter에서 처리를 하고 더 이상 Application으로 넘어갈 필요가 없으면 true로 리턴하면 됩니다.


### IMessageFilter 구현 및 사용
#### 구현
```c#
internal class CustomMessageFilter : IMessageFilter
{
    private ListBox ListBox { get; set; }

    public CustomMessageFilter(ListBox listBox)
    {
        this.ListBox = listBox;
    }

    public bool PreFilterMessage(ref Message m)
    {        
        if (m.Msg == 0x201)
        {
            ListBox.Items.Add($"선 처리!! | {m}");
            // return true;
        }

        return false;
    }
}
```

#### 사용
```c#
private IMessageFilter Filter { get; set; }

public Form1()
{
    InitializeComponent();
    Filter = new CustomMessageFilter(this.listBox1);
    Application.AddMessageFilter(Filter);
    //Application.RemoveMessageFilter(Filter);
}
```

### 결과
![result](/assets/img/posts/2021-12-24/result.png)

---

결과를 확인하면 Application에서 버튼을 클릭하기 전 마우스 클릭 이벤트가 먼저 Filter에서 처리된 것을 확인할 수 있습니다.

여기서 만약 PreFilterMessage 메서드에서 true로 리턴을 했다면 버튼 클릭에 대한 이벤트는 발생하지 않을 것 입니다.

Applicaion의 클래스 메서드인 AddMessageFilter를 사용했기 때문에 프로세스가 끝나기 전까지 모든 폼에 대해서 Filter가 적용이 됩니다.

Filter가 더 이상 필요가 없거나 특정 폼에서는 사용하지 않는다고하면 RemoveMessageFilter를 이용해 Filter를 해제 할 수 있습니다.

---

## Reference

[IMessageFilter Interface](https://docs.microsoft.com/ko-kr/dotnet/api/system.windows.forms.imessagefilter?view=windowsdesktop-6.0){:target="_blank"}

[한빛미디어 C# 메시지 처리](https://www.hanbit.co.kr/media/channel/view.html?cms_code=CMS8867818733&cate_cd=){:target="_blank"}
