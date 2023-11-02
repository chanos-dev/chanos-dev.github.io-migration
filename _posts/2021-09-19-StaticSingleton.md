---
date: 2021-09-19
layout: post
title:  "C# static class, singleton"
description: "static class, singleton"
image: /assets/img/posts/image/csharp.png
optimized_image: /assets/img/posts/image/csharp.png
category: C Sharp
tags:
  - C#
  - .NET
  - singleton
  - static class
author: chanos
---
>[Git Source](https://github.com/chanos-dev/blogcode/tree/master/21-0918){:target="_blank"}

---

코드 작성 시 정적 클래스와 싱글톤패턴을 사용하는 경우가 있는데 어떠한 차이점이 있는지 살펴보고자 포스트를 작성하게 됐다.

일단, 싱글톤패턴은 하나의 인스턴스를 만들어 재사용하는 것이고 정적 클래스는 인스턴스를 생성하지 않고 클래스명으로 바로 접근하여 사용하는 것이다.

비슷한 점으로는 외부에서 new 키워드를 이용하여 따로 인스턴스를 생성하지 않고 사용한다는 것과 정적 클래스 및 정적 변수를 사용함으로서 high-frequency heap에 할당되며 GC가 일어나지 않는다.

차이점은 다음과 같다.

| 차이점 | 싱글톤패턴 | 정적 클래스 |
| :- | :- | :- |
| 원리 | 하나의 인스턴스 생성 후 재사용 | 인스턴스를 생성하지 않고 사용 |
| 초기화 | 싱글톤 객체 처음 참조 시 초기화 된다. | CLR에 의해 자동으로 초기화 된다. |
| 인터페이스 구현 <br>및<br>클래스 상속 | 가능 | 불가능 |
| 메서드 매개변수 전달 | 가능 | 불가능 |
| 의존성 주입 | 가능 | 불가능 |
| 객체지향 규칙을 따르는지 | O | X | 

---

그렇다면 어떠한 기준에 따라 코드를 작성해야할까?

관련 정보를 찾아보던 중 확실하게 어떠한게 좋다라는 것은 없었고 가장 큰 차이는 `상태`를 갖고 있는지에 따라 달라지는 것 같다.

`상태`가 필요할 경우 싱글톤으로 그렇지 않은 경우 정적 클래스를 사용하는 것을 고려해볼 수 있다.

---

## Reference

[Singleton VS Static class in C#](https://dotnettutorials.net/lesson/singleton-vs-static-class/){:target="_blank"}