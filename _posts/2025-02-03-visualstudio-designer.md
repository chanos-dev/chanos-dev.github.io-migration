---
date: 2025-02-03
layout: post
title:  "Winforms 디자이너 보기를 했는데 코드가 실행된다?"
description: "winforms designer"
image: /assets/img/posts/image/visual_studio.png
optimized_image: /assets/img/posts/image/visual_studio.png
category: Visual Studio
tags:
  - Winforms
  - Visual Studio
author: chanos
---
> [Git Source](https://github.com/chanos-dev/blogcode/tree/master/25-0203){:target="_blank"}  

```
환경
- visual studio 2022
- .net framework 4.8
```

---

여러 클래스에 공통 로직이 존재하는 경우 부모 클래스에 해당 로직을 분리하고 상속 받아 설계를 할 수 있습니다.

Winforms에서도 서로 다른 Form이지만 공통 로직이 존재하는 경우 BaseForm과 같은 클래스로 분리하고 상속 받아서 처리할 수 있는데 이 때 발생할 수 있는 문제가 있습니다.

BaseForm에 abstract 키워드를 주게 됐을 때 디자이너가 정상적으로 표시되지 않기도 하고 때로는 생성자, 이벤트에 작성한 로직이 디자이너를 열었을 때 실행되기도 합니다.

[![error-abstract](/assets/img/posts/2025-02-03/error-abstract.png)](/assets/img/posts/2025-02-03/error-abstract.png){: data-lightbox="image"}

visual studio에서 디자이너가 표시되는 과정과 디자인 타임에서 코드가 실행되는 것을 막기 위한 방법을 알아보겠습니다.

---

visual studio에서 Form class의 인식과 디자이너가 표시되는 과정은 다음과 같습니다.

1. *.cs 파일의 첫번째 클래스가 `Form`을 상속 받고 있는지 확인한다.
  - Form을 상속 받고 있다면, `Solution Explorer`에 Form 표시가 뜨고 디자이너 보기가 가능하다.  
  [![form-explorer](/assets/img/posts/2025-02-03/form-explorer.png)](/assets/img/posts/2025-02-03/form-explorer.png){: data-lightbox="image"}
  - Form을 상속 받고 있지 않다면, `Solution Explorer`에 cs 표시가 뜨고 디자이너 보기가 불가능하다.  
  [![not-form-explorer](/assets/img/posts/2025-02-03/not-form-explorer.png)](/assets/img/posts/2025-02-03/not-form-explorer.png){: data-lightbox="image"}

2. 클래스에 `InitializeComponent` 메서드가 정의되어 있는지 확인한다.
  - 정의되어 있다면 디자이너를 표시하기 위해 해당 메서드를 `호출 하지 않고 정적 코드 분석`으로 각 Control을 배치한다.

3. 해당 클래스의 부모 클래스 생성자를 호출한다.
  - 부모 클래스의 생성자에 `InitializeComponent` 메서드가 있다면 호출되면서 자식 클래스의 디자이너에 `Inherited Controls`로 같이 표시된다.
  - 생성자 또는 `InitializeComponent`에서 이벤트 구독 로직이 있다면, 각 컨트롤이 구성될 때 이벤트가 호출된다.

  ---

앞서 언급했던 문제들이 발생하는 이유는 3번 때문입니다.

#### abstract 문제
abstract 키워드가 붙은 추상 클래스는 인스턴스화 할 수 없는 클래스로 생성자를 직접적으로 호출 할 수 없습니다. 그래서 디자이너를 표시할 때 에러가 발생하게 됩니다.

#### 생성자 또는 이벤트 로직 실행 문제
부모 클래스 생성자에서 `InitializeComponent`와 같은 메서드를 호출하거나 Form 이벤트인 Load, Shown에 이벤트를 구독한 경우 자식 클래스에서 디자이너가 구성되면서 이벤트가 호출 됩니다.

```cs
#BaseForm.cs
public class BaseForm : Form
{
    public BaseForm()
    {
        Load += (sender, e) => MessageBox.Show("Load Test");
    }
}

#MainForm.cs
public partial class MainForm : BaseForm
{
    public MainForm()
    {
        InitializeComponent();
    }
}
```

[![call-event](/assets/img/posts/2025-02-03/call-event.png)](/assets/img/posts/2025-02-03/call-event.png){: data-lightbox="image"}

이렇게 디자인 타임에서 로직이 호출되는 문제가 있어서 예기치 않은 오류를 접할 수 있습니다.  

부모 클래스 생성자에서 컨트롤 초기화 로직이나 Form의 Load, Shown등 이벤트 로직을 런타임에서만 동작하게 할 수 있습니다.

방법으로는 디자인 타임 여부를 확인할 수 있는 `Component.DesignMode`를 이용할 수 있습니다.

생성자에서는 `Component.DesignMode`를 사용할 수 없는데, getter 구현부를 살펴보면 다음과 같습니다.

```cs
private ISite site;
...
[Browsable(false)]
[DesignerSerializationVisibility(DesignerSerializationVisibility.Hidden)]
protected bool DesignMode => site?.DesignMode ?? false;
```

site 필드가 할당되고 나서야 확인이 가능한데 생성자에서는 container와 site가 구성되기 전이라 확인이 불가능합니다. 그러나 생성자 이후인 Load, Shown 이벤트에서는 DesignMode로 디자인 타임 확인이 가능합니다.

```cs
#BaseForm.cs
public class BaseForm : Form
{
    public BaseForm()
    {        
        // MainForm에서 디자이너 활성화 시

        // 생성자에서 false로 반환
        MessageBox.Show($"Constructor : {DesignMode}");

        //  - Load -> true로 반환
        //  - Shown -> true로 반환     
        Load += (sender, e) => MessageBox.Show($"Load : {DesignMode}");
        Shown += (sender, e) => MessageBox.Show($"Shown : {DesignMode}");        

        // 런타임 실행 시 전부 false로 반환
    }
}

#MainForm.cs
public partial class MainForm : BaseForm
{
    public MainForm()
    {
        InitializeComponent();
    }
}
```

생성자에서는 DesignMode로 디자인 타임인지 확인이 불가능한데, 다른 방법으로 디자인 타임인지 확인할 수 있습니다.

`LicenseManager.UsageMode`와 `Process.GetCurrentProcess().ProcessName`를 이용하는 방법입니다.

`LicenseManager`는 라이선스가 필요한 컴포넌트를 개발할 때 사용하는 정적 클래스인데, LicenseManager의 정적 속성인 UsageMode 열거형을 사용하면 생성자에서 `LicenseUsageMode.Designtime`을 받아올 수 있습니다.

그러나 생성자 이후 이벤트에서는 다른 값으로 반환이 되니 주의해야 합니다.

```cs
#BaseForm.cs
public class BaseForm : Form
{
    public BaseForm()
    { 
        // MainForm에서 디자이너 활성화 시

        // 생성자에서 Designtime으로 반환
        MessageBox.Show($"Constructor : {LicenseManager.UsageMode}");
              
        //  - Load -> Designtime으로 반환
        //  - Shown -> Runtime으로 반환        
        Load += (sender, e) => MessageBox.Show($"Load : {LicenseManager.UsageMode}");
        Shown += (sender, e) => MessageBox.Show($"Shown : {LicenseManager.UsageMode}");

        // 런타임 실행 시 전부 Runtime으로 반환
    }
}

#MainForm.cs
public partial class MainForm : BaseForm
{
    public MainForm()
    {
        InitializeComponent();
    }
}
```

`Process.GetCurrentProcess().ProcessName`를 이용하는 방법으로는 visual studio의 프로세스가 `devenv.exe`로 실행되는데 이 프로세스가 부모 클래스에서 작성한 로직을 호출합니다.

부모 클래스에서 발생한 MessageBox를 spy++로 추적해보면 아래와 같이 devenv가 호출하고 있는 것을 확인할 수 있습니다.

[![shown-process](/assets/img/posts/2025-02-03/shown-process.png)](/assets/img/posts/2025-02-03/shown-process.png){: data-lightbox="image"}

[![shown-spy](/assets/img/posts/2025-02-03/shown-spy.png)](/assets/img/posts/2025-02-03/shown-spy.png){: data-lightbox="image"}

Process 탭으로 이동해서 Process ID를 클릭해서 이동하면

[![devenv-spy](/assets/img/posts/2025-02-03/devenv-spy.png)](/assets/img/posts/2025-02-03/devenv-spy.png){: data-lightbox="image"}

그래서 `ProcessName`이 `devenv`인 경우 디자인 타임으로도 확인할 수 있습니다.

```cs
#BaseForm.cs
public class BaseForm : Form
{
    public BaseForm()
    {        
       // MainForm에서 디자이너 활성화 시 `devenv` 반환
       MessageBox.Show($"Constructor : {Process.GetCurrentProcess().ProcessName}");
       Load += (sender, e) => MessageBox.Show($"Load : {Process.GetCurrentProcess().ProcessName}");
       Shown += (sender, e) => MessageBox.Show($"Shown : {Process.GetCurrentProcess().ProcessName}");
    }
}

#MainForm.cs
public partial class MainForm : BaseForm
{
    public MainForm()
    {
        InitializeComponent();
    }
}
```

이렇게 3가지 방법으로 디자인 타임을 확인할 수 있는데 Process를 이용하는 방법은 권장되지 않는 방법으로 생성자에서 확인이 필요하다면 `LicenseManager.UsageMode`를 사용하고 이벤트에서 확인이 필요하다면 `Component.DesignMode`를 이용하면 될 것 같습니다.

## Reference

[Identifying the Run-Time and the Design Mode](https://dotnetfacts.blogspot.com/2009/01/identifying-run-time-and-design-mode.html){:target="_blank"} 