---
date: 2024-02-14
layout: post
title:  "Windows Custom URL Scheme"
description: "custom url scheme"
image: /assets/img/posts/image/cs.png
optimized_image: /assets/img/posts/image/cs.png
category: CS
tags:
  - Windows Application
  - URL
  - Registry
author: chanos
---
> [Git Source](https://github.com/chanos-dev/blogcode/tree/master/24-0214){:target="_blank"}  

---

안녕하세요. chanos입니다. 🐯

가끔 노션과 같은 앱을 웹에서 사용 시 아래와 같은 alert이 표시되는 것을 확인할 수 있습니다.

![notion](/assets/img/posts/2024-02-14/notion.png)

`Notion 열기` 버튼을 클릭하는 경우, Windows에 설치되어 있는 응용 프로그램이 실행되게 되는데 어떻게 가능한 걸까요?

앱 개발자들에게는 `DeepLink`로 알려져 있는  `Custom URL Scheme`를 이용한 방법입니다.

## Custom URL Scheme ?

보통 URL을 살펴보면 `://`까지가 URL Scheme입니다.

```
https://www.microsoft.com -> https
ftp://192.168.0.2 -> ftp
```

[official](https://www.iana.org/assignments/uri-schemes/uri-schemes.xhtml)하게 관리되는 scheme가 있지만 사용자가 직접 scheme를 정의할 수 있습니다.

대부분 OS에서 url scheme를 직접 정의할 수 있고, windows에서는 registry에서 관리가 됩니다.

custom url scheme를 만나면 해당 scheme로 등록되어 있는 응용 프로그램과 연결이 됩니다.

노션 또한 registry에서 url scheme를 관리하고 있고 보통 설치 파일에서 레지스트리 등록을 진행합니다.

![notion-reg](/assets/img/posts/2024-02-14/notion-reg.png)

## Custom URL Scheme 등록

[Registering an Application to a URI Scheme](https://learn.microsoft.com/en-us/previous-versions/windows/internet-explorer/ie-developer/platform-apis/aa767914(v=vs.85)?redirectedfrom=MSDN)에서 확인할 수 있고 다음 절차로 레지스트리에 등록합니다.

1. `HKEY_CLASSES_ROOT` 하위에 custom scheme로 사용할 key를 추가 합니다.
  ![add-key](/assets/img/posts/2024-02-14/add-key.png)

2. 생성한 key 밑에 문자열 값을 추가하고 name을 `URL Protocol`로 설정합니다.
  ![add-str-value](/assets/img/posts/2024-02-14/add-str-value.png)
  ![add-str-value-2](/assets/img/posts/2024-02-14/add-str-value-2.png)

3. 생성한 key 밑에 `{key}.shell.open.command` 구조로 하위 키를 생성합니다.
  ![add-sub-key](/assets/img/posts/2024-02-14/add-sub-key.png)

4. command key의 `default value`에 실행할 응용 프로그램 절대 경로를 입력합니다. 만약 URL 값을 인수로 받고 싶다면 경로 뒤에 `"%1"`을 추가합니다.
  ![add-command](/assets/img/posts/2024-02-14/add-command.png)

5. 정의한 custom url scheme를 이용해 응용 프로그램을 호출할 수 있고, 아규먼트에는 url이 들어가게 됩니다.
  ![execute](/assets/img/posts/2024-02-14/execute.png)
```cs
// chanos://hello, world!
static void Main(string[] args)
{
  Console.WriteLine($"Hello!! {string.Join(" / ", args)}");
}
```
  ![cmd](/assets/img/posts/2024-02-14/cmd.png)

## Reference

[Custom URI Scheme 만들기](https://nsinc.tistory.com/77){:target="_blank"} 