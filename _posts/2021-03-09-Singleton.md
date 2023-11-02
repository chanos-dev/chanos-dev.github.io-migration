---
date: 2021-03-09
layout: post
title:  "C# Singleton Pattern"
description: "Singleton Pattern"
image: /assets/img/posts/image/csharp.png
optimized_image: /assets/img/posts/image/csharp.png
category: C Sharp
tags:
  - C#
  - .NET
  - Singleton
  - Design pattern
author: chanos
---
>[Git Source](https://github.com/chanos-dev/blogcode/tree/master/21-0309)

---

> <b> Singleton Pattern </b> 👤
 
- 싱글톤은 애플리케이션이 실행 된 이후 단 한개의 인스턴스만을 생성하고자 할 떄 사용하는 패턴이다.

- 흔하게 쓰이는 디자인 패턴이며, 주로 환경설정과 같이 하나로 관리할 때 사용한다.

- C#에서는 싱글톤을 구현하는 방법이 여러가지가 있는데 이번 포스트에서 확인해보자.

#### 1 - UnsafeSingleton
- 기본적으로 다음과 같이 싱글톤을 구현할 수 있는데 이렇게 구현하게 되면 멀티스레드 환경에서 문제가 발생한다.  
두 개 이상의 스레드에서 인스턴스에 접근을 할 때 각기 다른 인스턴스가 생성될 수 있기 때문에 싱글톤에 위반되는 행위가 발생한다.

```c# 
class UnsafeSingleton : Singleton
{ 
	private static UnsafeSingleton _instance;

	public static UnsafeSingleton Instance
	{
		get
		{
			if (_instance is null)
			{
				_instance = new UnsafeSingleton();
			}

			return _instance;
		}
	} 

	protected UnsafeSingleton() => Console.WriteLine("Create UnsafeSingleton instance!!"); 
}
```

#### 2 - SafeSingleton
- 1번은 스레드에 안전하지 않으므로 스레드에 안전하게 구현하면 2번과 같다.  
하지만 2번에도 문제가 있으니.. 인스턴스를 참조할 때 마다 lock으로 인해 성능 저하가 발생할 수 있다.

```c#
class SafeSingleton : Singleton
{
	private static object _thislock = new object();
	private static SafeSingleton _instance;

	public static SafeSingleton Instance
	{
		get
		{
			lock (_thislock)
			{
				if (_instance is null)
				{
					_instance = new SafeSingleton();
				}
			}

			return _instance;
		}
	}

	protected SafeSingleton() => Console.WriteLine("Create SafeSingleton instance!!"); 
}
```

#### 3 - DCLSingleton
- 2번의 lock으로 인한 성능저하를 막고자 나온 방법이 Double Checked Lock이다.  

```c#
//Double Checked Locking
class DCLSingleton : Singleton
{
	private static object _thislock = new object();
	private static DCLSingleton _instance;

	public static DCLSingleton Instance
	{
		get
		{
			if (_instance is null)
			{
				lock (_thislock)
				{
					if (_instance is null)
					{
						_instance = new DCLSingleton();
					}
				}
			}

			return _instance;
		}
	}

	protected DCLSingleton() => Console.WriteLine("Create DCLSingleton instance!!"); 
}
```

#### 4 - StaticSingleton
- static constructor을 이용하여 구현할 수도 있다.

```c#
    class StaticSingleton : Singleton
    {
        private static readonly StaticSingleton _instance;

        static StaticSingleton()
        {
            _instance = new StaticSingleton();
        }

        public static StaticSingleton Instance => _instance;

        protected StaticSingleton() => Console.WriteLine("Create StaticSingleton instance!!"); 
    }
```

#### 5 - LazySingleton
- Lazy를 이용하여 구현할 수도 있는데, Lazy는 즉시 생성이 아닌 접근하려 할 때 생성이 된다.

```c#
class LazySingleton : Singleton
{
	private static readonly Lazy<LazySingleton> _instance = new Lazy<LazySingleton>(() => new LazySingleton());

	public static LazySingleton Instance => _instance.Value;

	protected LazySingleton() => Console.WriteLine("Create LazySingleton instance!!"); 
} 
``` 

![singleton](/assets/img/posts/2021-03-09/singleton.png)