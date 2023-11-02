---
date: 2021-04-28
layout: post
title:  "C# 한글 자동완성"
description: "AutoComplete"
image: /assets/img/posts/image/csharp.png
optimized_image: /assets/img/posts/image/csharp.png
category: C Sharp
tags:
  - C#
  - .NET
  - Hangul
  - AutoComplete
  - Winforms
author: chanos
---
>[Git Source](https://github.com/chanos-dev/blogcode/tree/master/21-0428)

--- 

한글의 유니코드를 사용하여 사용자 입력시 정규식을 통해 해당되는 검색어들을 보여준다.

현재 사전이나 컴퓨터 한글 코드에서 정의된 한글의 배열 순서는 다음과 같다.

자음(19자) : ㄱ ㄲ ㄴ ㄷ ㄸ ㄹ ㅁ ㅂ ㅃ ㅅ ㅆ ㅇ ㅈ ㅉ ㅊ ㅋ ㅌ ㅍ ㅎ

모음(21자) : ㅏ ㅐ ㅑ ㅒ ㅓ ㅔ ㅕ ㅖ ㅗ ㅘ ㅙ ㅚ ㅛ ㅜ ㅝ ㅞ ㅟ ㅠ ㅡ ㅢ ㅣ

받침(27자) : ㄱ ㄲ ㄳ ㄴ ㄵ ㄶ ㄷ ㄹ ㄺ ㄻ ㄼ ㄽ ㄾ ㄿ ㅀ ㅁ ㅂ ㅄ ㅅ ㅆ ㅇ ㅈ ㅊ ㅋ ㅌ ㅍ ㅎ

위에서 얘기한 음절 단위를 통해 완성형 한글을 만들어보면 `가`를 시작으로 `힣`으로 끝나는 것을 알 수 있다.

유니코드에서 `가`는 U+AC00(44,032)으로 `힣`은 U+D7A3(55,203)이다.

`자음` + `모음(21)` + `받침(27)`으로 하나의 자음에 총 587개의 문자가 나오며 44,032(`가`)에서 587을 더하면 마지막 글자인 `깋`이 나오고 +588을 하면 다음 자음인 `까`가 나온다.

위의 기준을 통해 입력된 텍스트를 유니코드와 정규식을 통해 검색을 하는 간단한 코드 및 실행 결과이며, Reference에 [WPF & C# - 초성검색 한글자동완성](https://insurang.tistory.com/364){:target="_blank"} 블로그를 참고하여 코드를 작성하였다.

```c#
class TextSearch
{
    char[] chr = { 'ㄱ', 'ㄲ', 'ㄴ', 'ㄷ', 'ㄸ', 'ㄹ', 'ㅁ', 'ㅂ', 'ㅃ', 'ㅅ', 'ㅆ', 'ㅇ', 'ㅈ', 'ㅉ', 'ㅊ', 'ㅋ', 'ㅌ', 'ㅍ', 'ㅎ' };
    string[] str = { "가", "까", "나", "다", "따", "라", "마", "바", "빠", "사", "싸", "아", "자", "짜", "차", "카", "타", "파", "하" }; 
    int[] chrint = { 44032, 44620, 45208, 45796, 46384, 46972, 47560, 48148, 48736, 49324, 49912, 50500, 51088, 51676, 52264, 52852, 53440, 54028, 54616, 55204 }; 

    public List<string> SearchWord(List<string> contents, string word, out string searchPattern)
    {
        var pattern = new StringBuilder(); 

        for (int i = 0; i < word.Length; i++)
        { 
            if (word[i] >= 'ㄱ' && word[i] <= 'ㅎ')
            {
                for (int j = 0; j < chr.Length; j++)
                {
                    if (word[i] == chr[j])
                    { 
                        pattern.Append($"[{str[j]}-{(char)(chrint[j + 1] - 1)}]");
                    }
                }
            } 
            else if (word[i] >= '가')
            {
                int magic = ((word[i] - '가') % 588);

                magic = 27 - (magic % 28);

                pattern.Append($"[{word[i]}-{(char)(word[i] + magic)}]"); 
            }  
            else if (word[i] >= 'A' && word[i] <= 'z')
            { 
                bool isLower = word[i] >= 'a' ? true : false;

                pattern.Append($"[{word[i]}{(char)(isLower ? word[i] - 32 : word[i] + 32)}]");
            } 
            else if (word[i] >= '0' && word[i] <= '9')
            {
                pattern.Append(word[i]);
            }
        }

        searchPattern = pattern.ToString();
        return contents.Where(e => Regex.IsMatch(e.ToString(), pattern.ToString())).ToList();
    }
}
```

```c#
// Sample Data
빨강 양귀비(Papaver) : 위로
수레국화(Corn Flower) : 행복
수박풀(Flower of an Hour) : 아가씨의 아름다운 자태
옥수수(Corn) : 재보(財寶)
엘리카(Heath) : 고독
능소화(Trumpet Flower) : 명예
석류(Pomagranate) : 원숙한 아름다움
진달래(Azalea) : 사랑의 희열
시스터스(Cistus) : 인기
이끼(Moss) : 모성애
빨강무늬제라늄(Geranium Zonal) : 위안
협죽도(Oleander) : 위험
골든 로드(Golden Rod) : 경계
저먼더(Wall Germander) : 경애
해바라기(Sun Flower) : 광휘
타마린드(Tamarindus) : 사치
튤립나무(Tulip-Tree) : 전원의 행복
접시꽃(Holly Hock) : 열렬한 사랑
로사 캠피온(Rosa Campion) : 성실
프리지아(Freesia) : 순결
짚신나물(Agrimony) : 감사
스피리아(Sprirea) : 노력
서양종 보리수(Lime Tree, Linden) : 부부애
금잔화(Calendula) : 이별의 슬픔
안스륨(Flaming Flower) : 사랑에 번민하는 마음
하이포시스 오리어(Hypoxis Aurea) : 빛을 찾다
고비(Osumunda) : 몽상
에린지움(Eryngium) : 비밀스런 애정
꽃담배(Flowering Tabacco Plant) : 그대 있어 외롭지 않네
저먼더(Wall Germander) : 담백
토끼풀(Clover) : 약속
```

- 결과
![result1](/assets/img/posts/2021-04-28/result1.png)

![result2](/assets/img/posts/2021-04-28/result2.png)

![result3](/assets/img/posts/2021-04-28/result3.png)

---

## Reference

[한글 자모의 배열 순서](https://www.korean.go.kr/nkview/news/12/128.htm){:target="_blank"}

[WPF & C# - 초성검색 한글자동완성](https://insurang.tistory.com/364){:target="_blank"}

- 블로그에 있는 원문주소가 사라져서 참고했던 블로그를 대신 올린다.