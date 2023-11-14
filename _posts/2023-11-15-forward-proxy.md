---
date: 2023-11-15
layout: post
title:  "C# proxy 설정 (+인증)"
description: "forward proxy"
image: /assets/img/posts/image/csharp.png
optimized_image: /assets/img/posts/image/csharp.png
category: C Sharp
tags:
  - C#
  - .NET
  - proxy
  - forward proxy
  - squid
author: chanos
---
> [Git Source](https://github.com/chanos-dev/blogcode/tree/master/23-1115){:target="_blank"}  
> [Drawio](https://github.com/chanos-dev/chanos-dev.github.io/tree/master/document/2023-11-15/forward-proxy.drawio)

---

안녕하세요. chanos입니다. 🐯

C#에서 **forward proxy**를 설정하고 HTTP를 호출하는 방법에 대해 알아보겠습니다.

## forward proxy란?

클라이언트가 인터넷에 직접 접근하는 것이 아니라 forward proxy가 대신 요청을 받고 인터넷에 연결하여 서버 응답을 클라이언트에게 전달 해주는 역할을 합니다.

보통 forward proxy는 같은 내부망의 여러 클라이언트 앞에 위치해 있습니다.

![forward-proxy](/assets/img/posts/2023-11-15/forward-proxy.png)

### forward proxy 사용 시 다음과 같은 이점이 있습니다.

#### 클라이언트 보안
공공기관 또는 학교와 같은 곳에서 클라이언트의 제한적인 인터넷 사용을 위해 특정 사이트에 접근하는 것을 막을 수 있습니다.

#### 캐싱
클라이언트의 요청이 있을 때 해당 요청을 캐싱하여 다른 클라이언트에서 같은 요청이 들어오면 캐싱된 데이터를 전달하여 성능 향상 및 서버에 부하를 줄일 수 있습니다.

#### 클라이언트 IP 주소 암호화
클라이언트가 forward proxy를 이용하여 요청하는 경우 외부 서버에서는 IP 주소를 추적하려고 해도 클라이언트의 IP가 아닌 요청한 proxy의 IP가 보여 클라이언트 IP를 감춰주는 보안 효과가 있습니다. 


## 테스트를 위한 Squid Proxy 구축

forward proxy 연결 테스트를 위해 Squid docker image를 이용하여 간단하게 구축을 진행합니다.

```shell
# 인증을 위한 htpasswd 생성
htpasswd -c ./password test1
New password: test1
Re-type new password: test1
Adding password for user test1
```

```conf
# squid.conf

auth_param basic program /usr/lib/squid/basic_ncsa_auth /etc/squid/passwords
auth_param basic realm Squid proxy-caching web server
auth_param basic credentialsttl 2 hours
auth_param basic casesensitive off

http_port 3128

cache_dir ufs /var/spool/squid 100 16 256
cache_log /var/log/squid/cache.log
access_log /var/log/squid/access.log

acl auth proxy_auth REQUIRED

# 도메인 제한
acl domains dstdomain .microsoft.com

http_access allow auth domains
http_access deny !domains
```

```yml
version: '3.9'
services:
  squid:
    image: ubuntu/squid
    container_name: squid
    volumes:
      - ${PWD}/squid.conf:/etc/squid/squid.conf
      - ${PWD}/password:/etc/squid/passwords
    ports:
      - 3128:3128
```

## C# proxy 설정 후 호출

개발 환경은 .NET6로 진행했습니다.

C#에서 proxy를 사용하기 위해서는 **WebProxy** 객체를 생성해야 합니다.

```csharp
using System.Net;

WebProxy proxy = new("192.168.0.105", 3128)
{
    Credentials = new NetworkCredential("test1", "test1")
};
```

Credentials 속성을 통해 proxy 인증 정보를 구성할 수 있습니다.

그리고 **HttpClient**에서 실질적인 소켓 관리를 담당하는 **HttpClientHandler** 객체를 생성해 proxy를 설정해 주고 HTTP 요청 테스트를 진행합니다.

proxy 인증에 실패한 경우 **407** 예외가 발생하고 허용되지 않은 도메인에 요청한 경우 **403** 예외가 발생합니다.

```csharp
HttpClientHandler handler = new()
{
    Proxy = proxy,
};

try
{
    using HttpClient client = new(handler);
    using var response = await client.GetAsync("https://microsoft.com/");
    //deny
    using var responseDeny = await client.GetAsync("https://www.naver.com/");
}
catch (HttpRequestException ex)
{
    // deny exception 403
    // auth exception 407
    Console.WriteLine(ex);
}
```


## proxy log 확인

```text
# cd /var/log/squid && watch -n 1 "tail -n 10 access.log"

Every 1.0s: tail -n 10 access.log                                                 d075a4db7674: Tue Nov 14 16:34:09 2023

1699979204.488   3000 172.22.0.1 TCP_TUNNEL/200 7724 CONNECT microsoft.com:443 test1 HIER_DIRECT/20.112.250.133 -
1699979204.488   2193 172.22.0.1 TCP_TUNNEL/200 5792 CONNECT www.microsoft.com:443 test1 HIER_DIRECT/23.40.45.184 -
1699979611.051      0 172.22.0.1 TCP_DENIED/407 3857 CONNECT microsoft.com:443 - HIER_NONE/- text/html
1699979611.819      0 172.22.0.1 TCP_DENIED/407 3873 CONNECT www.microsoft.com:443 - HIER_NONE/- text/html
1699979611.922      0 172.22.0.1 TCP_DENIED/407 3857 CONNECT www.naver.com:443 - HIER_NONE/- text/html
1699979611.925      0 172.22.0.1 TCP_DENIED/403 3805 CONNECT www.naver.com:443 test1 HIER_NONE/- text/html
1699979611.944    884 172.22.0.1 TCP_TUNNEL/200 7724 CONNECT microsoft.com:443 test1 HIER_DIRECT/20.112.250.133 -
1699979611.944    121 172.22.0.1 TCP_TUNNEL/200 5792 CONNECT www.microsoft.com:443 test1 HIER_DIRECT/23.40.45.184 -
1699979619.966      0 172.22.0.1 TCP_DENIED/407 3857 CONNECT microsoft.com:443 - HIER_NONE/- text/html
1699979619.991      2 172.22.0.1 TCP_DENIED/407 3953 CONNECT microsoft.com:443 test1 HIER_NONE/- text/html
```

## Reference

[🌐 Reverse Proxy / Forward Proxy 정의 & 차이 정리](https://inpa.tistory.com/entry/NETWORK-%F0%9F%93%A1-Reverse-Proxy-Forward-Proxy-%EC%A0%95%EC%9D%98-%EC%B0%A8%EC%9D%B4-%EC%A0%95%EB%A6%AC){:target="_blank"} 