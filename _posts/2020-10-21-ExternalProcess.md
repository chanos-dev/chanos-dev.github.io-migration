---
date: 2020-10-21
layout: post
title:  "C# 응용프로그램 실행하기"
description: "Process.Start"
image: /assets/img/posts/image/csharp.png
optimized_image: /assets/img/posts/image/csharp.png
category: C Sharp
tags:
  - C#
  - .NET
author: chanos
---
>[Git Source](https://github.com/chanos-dev/blogcode/tree/master/20-1021)

--- 

![program](/assets/img/posts/2020-10-21/program.png)

```c#
private void button_Process_Click(object sender, EventArgs e)
{
    if (string.IsNullOrEmpty(textBox_Process.Text))
        return;

    Process.Start(textBox_Process.Text);
}

private void button_args_Click(object sender, EventArgs e)
{
    if ((string.IsNullOrEmpty(textBox_args.Text)) || (string.IsNullOrEmpty(textBox_args1.Text)))
        return;

    Process.Start(textBox_args.Text, textBox_args1.Text);                        
}
```

### - Process.Start -


```c#
using System.Diagnostics; //추가
...
//응용프로그램 실행
Process.Start("http://www.google.co.kr"); //인터넷 실행

Process.Start("C:\Windows\System32"); //폴더 실행

Process.Start("cmd.exe"); //cmd 실행

//arguments 전달
Process.Start("cmd.exe", "\c ping 8.8.8.8); //cmd - ping 8.8.8.8 커맨드 실행
```
