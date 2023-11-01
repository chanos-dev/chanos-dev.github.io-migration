---
date: 2020-10-15
layout: post
title:  "ES ElasticSearch (1)"
description: "ElasticSearch란"
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
    - ES ElasticSearch (1) 👈
    - [ES ElasticSearch (2)]({{ site.url }}/ElasticSearch(2)/)
    - [ES ElasticSearch (3)]({{ site.url }}/ElasticSearch(3)/)

--- 
> <b>ElasticSearch</b> 👀

- Apache Lucene으로 구현한 Restful API 기반의 검색엔진이다.
    - Lucene - 풀텍스트 검색 엔진을 만들 수 있는 자바 라이브러리.
- 방대한 양의 데이터를 신속하게 처리 가능하다.
- 거의 실시간(NRT, Near Real Time)으로 저장, 검색, 분석이 가능하다.
- 고가용성 보장, 쉬운 스케일 아웃

> <b>ES</b>는 역색인 구조 📑
 
| Document  | Value        |
| :------   | :----------- |
| Document1 | 악어는 무섭다 |
| Document2 | 악어의 눈물   | 
| Document3 | 악어와 악어새 |

- 다음과 같이 데이터가 있을 경우 ES는 데이터를 역색인 구조로 데이터를 관리한다.

| Value  | Document                        |
| :------| :-----------                    |
| 악어   | Document1, Document2, Document3 |
| 무섭다 | Document1                       |
| 눈물   | Document2                       |
| 악어새 | Document3                       |

- Hashtable의 구조를 갖고 있기 때문에 O(1)의 시간복잡도를 가져 빠른 속도로 검색을 할 수 있다.

> <b>ES</b> <-> <b>RDBMS</b> ❓

- ES를 RDBMS의 다음과 같이 비교하여 생각하면 조금 쉽게 접근할 수 있을것같다.

| ES  | RDBMS        |
| :------   | :------ |
| Index | Database |
| Type | Table   | 
| Document | Row |
| Field | Column |
| Mapping | Schema |

- ES
    - Document간의 Join이 불가능하다.
    - 트랜잭션이 제공되지 않는다.
    - Elastic Search 6.x 버전 이후 부터는 type이 없어졌다. 