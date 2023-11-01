---
date: 2020-11-09
layout: post
title:  "ES ElasticSearch (3)"
description: "ElasticSearch 형태소 분석기"
image: /assets/img/posts/image/elasticsearch.png
optimized_image: /assets/img/posts/image/elasticsearch.png
category: ElasticSearch
tags:
  - Elasticsearch
  - ES
author: chanos
---
- 목차   
    - [ES 윈도우 ElasticSearch 설치]({{ site.url }}/ElasticSearchInstall-Windows/)
    - [ES ElasticSearch (1)]({{ site.url }}/ElasticSearch(1)/)
    - [ES ElasticSearch (2)]({{ site.url }}/ElasticSearch(2)/)
    - ES ElasticSearch (3) 👈

--- 
> <b>ElasticSearch analysis-nori</b> ✏

- 한글형태소 분석기는 `아리랑`, `은전한닢`, `Open korean text`, `Nori`가 있다. 그 중 ElasticSearch에서 개발하여 제공하는 `Nori`를 설치하여 사용해보기로 했다.

- `Nori`는 루씬을 기반으로 만들어졌으며, 은전한닢에서 사용하는 사전을 재가공하여 사용하고 있다.

> <b> analysis-nori 설치 </b> 🔧

```
C:\elasticsearch-7.9.2-windows-x86_64\elasticsearch-7.9.2\bin>elasticsearch-plugin install analysis-nori

//result
-> Installing analysis-nori
-> Downloading analysis-nori from elastic
[=================================================] 100%??
-> Installed analysis-nori
```

- 다음과 같은 명령어를 실행하면 `analysis-nori`를 `install`한다.

- 설치가 완료 됐으면 Elasticsearch Service를 재실행하여 정상적으로 Nori Plugin이 설치 되었는지 확인한다.

> <b> analysis-nori 확인하기 </b> ✔

```
//URL - GET Method
http://127.0.0.1:9200/_analyze

//RequestBody
{
    "tokenizer": "nori_tokenizer",
    "text":  "모두들 화이팅"
}

//Response
{
    "tokens": [
        {
            "token": "모두",
            "start_offset": 0,
            "end_offset": 2,
            "type": "word",
            "position": 0
        },
        {
            "token": "들",
            "start_offset": 2,
            "end_offset": 3,
            "type": "word",
            "position": 1
        },
        {
            "token": "화이팅",
            "start_offset": 4,
            "end_offset": 7,
            "type": "word",
            "position": 2
        }
    ]
}
```
 
- 다음과 같이 Get api 요청을 보내고, text의 문자가 토큰화 됐으면 성공!