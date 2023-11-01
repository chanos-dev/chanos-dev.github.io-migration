---
date: 2021-09-21
layout: post
title:  "Visual Studio Code Snippet"
description: "Code Snippet"
image: /assets/img/posts/image/visual_studio.png
optimized_image: /assets/img/posts/image/visual_studio.png
category: Visual Studio
tags:
  - C#
  - .NET
  - Visual Studio
  - Code Snipeet
author: chanos
---
Code Snippet은 클래스, 조건문 또는 반복문 등의 양식을 코드상에 빠르게 작성하기 위해 사용되는 일종의 매크로 기능이다.

예를 들면 MessageBox.Show("")와 같은 구문을 전체 타이핑을 하지않고 mbox라는 키워드를 입력 후 탭(tab)키를 누르면 자동으로 MessageBox.Show("")구문이 완성되는 것이다.

![mbox](/assets/img/posts/2021-09-21/mbox.png)

기본적으로 MS에서 제공하는 코드 조각은 [여기](https://docs.microsoft.com/ko-kr/visualstudio/ide/visual-csharp-code-snippets?view=vs-2017){:target="_blank"}에서 확인이 가능하다.

평소에 많이 쓰는 구문들이 이미 제공되고 있기 때문에 해당 내용을 확인 후 실제 코드 작성 시 사용한다면 생산성을 크게 높일 수 있을 것 같다.

그러나 코드 블럭을 사용자가 직접 만들고 싶은 경우가 있을 것이다.

VS에서 직접 만든 코드 블럭을 불러와서 사용할 수 있게 메뉴로 제공되고 있다.

`도구` -> `코드 조각 관리자`에서 확인이 가능하며 단축키는 `Ctrl+K, Ctrl+B`이다

![menu](/assets/img/posts/2021-09-21/menu.png)

기본적인 코드 블럭 템플릿은 .xml 형식이며 포맷은 다음과 같다

```xml
<?xml version="1.0" encoding="utf-8"?>
<CodeSnippets xmlns="http://schemas.microsoft.com/VisualStudio/2005/CodeSnippet">
    <CodeSnippet Format="1.0.0">
        <Header>
            <Title></Title>
        </Header>
        <Snippet>
            <Code Language="">
                <![CDATA[]]>
            </Code>
        </Snippet>
    </CodeSnippet>
</CodeSnippets>
```

아래 형식은 이번 포스트에서 매개변수와 리턴형식이 없는 메서드를 만들어보고자 하는 형식이다.

```c#
private void Foo()
{

}
```

위에서 제공되는 기본 포맷에서 추가 사항을 적용하면 다음과 같은 xml 형식이 나온다.

```xml
<?xml version="1.0" encoding="utf-8"?>
<CodeSnippets xmlns="http://schemas.microsoft.com/VisualStudio/2005/CodeSnippet">
    <CodeSnippet Format="1.0.0">
        <Header>
            <Title>기본 Method 정의</Title>
            <Description>코드 블럭 사용자 기본 Method 정의 테스트</Description>
            <shortcut>mt</shortcut>
        </Header>
        <Snippet>
            <Declarations>
                <Literal>
                    <ID>accessType</ID>
                    <ToolTip>접근 제한자</ToolTip>
                    <Default>private</Default> 
                </Literal>
                <Literal>
                    <ID>methodName</ID>
                    <ToolTip>메서드 명</ToolTip>
                    <Default>Foo</Default> 
                </Literal> 
            </Declarations>
            <Code Language="csharp">
                <![CDATA[$accessType$ void $methodName$()
                {
                    $end$
                }]]>
            </Code>
        </Snippet>
    </CodeSnippet>
</CodeSnippets>
```

기본 포맷 형식에는 없는 태그들이 추가되었는데 태그 내용을 참고해서 코드 블럭을 만들 때 참고하면 될 것 같다. 보다 정확한 정보는 [여기](https://docs.microsoft.com/ko-kr/visualstudio/ide/code-snippets-schema-reference?view=vs-2019){:target="_blank"}에서 확인이 가능하다.

| 태그 | 내용 |
| :- | :- |
| Title | 코드 블럭의 이름 |
| Description | 인텔리센스에서 보여지는 코드 블럭에 대한 내용 |
| shortcut | 코드 상에서 스니펫을 사용하기 위한 단축키 |
| ID | 코드 블럭에서 사용될 변수 명, 사용은 ID 앞 뒤로 '$'를 붙인다.|
| ToolTip | 인텔리센스에서 보여질 ID에 대한 내용 |
| Default | 코드 블럭 사용 시 ID 변수의 기본 값 |
| Code Language | 사용언어 - 제공되는 언어 (vb, csharp, cpp, xaml, xml, js, ts, sql, html) |
| <![CDATA["내용"]]> | "내용"에 사용할 코드블럭 소스 위에서 정의된 ID 값을 사용하려면 $ID$로 작성 |
| $end$ | 이 키워드가 있으면 코드 블럭 호출 시 $end$ 위치로 커서가 이동한다. (기본 예약어) |

작성된 *.snippet 파일을 `코드 조각 관리자`에서 가져오기로 가져온 다음 코드 상에서 지정한 shortcut을 입력하면 인텔리센스에서 나오게 된다.

![load](/assets/img/posts/2021-09-21/load.png)

![custom](/assets/img/posts/2021-09-21/custom.png)

![custom2](/assets/img/posts/2021-09-21/custom2.png)

## Reference

[c# 코드 조각](https://docs.microsoft.com/ko-kr/visualstudio/ide/visual-csharp-code-snippets?view=vs-2017){:target="_blank"}

[연습: 코드 조각 만들기](https://docs.microsoft.com/ko-kr/visualstudio/ide/walkthrough-creating-a-code-snippet?view=vs-2019){:target="_blank"}

[코드 조각 스키마 참조](https://docs.microsoft.com/ko-kr/visualstudio/ide/code-snippets-schema-reference?view=vs-2019){:target="_blank"}