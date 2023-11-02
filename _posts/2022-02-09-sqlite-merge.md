---
date: 2022-02-09
layout: post
title:  "C# sqlite merge"
description: "sqlite merge"
image: /assets/img/posts/image/csharp.png
optimized_image: /assets/img/posts/image/csharp.png
category: C Sharp
tags:
  - C#
  - .NET
  - sqlite
author: chanos
---
>[Git Source](https://github.com/chanos-dev/blogcode/tree/master/22-0208){:target="_blank"}

---

안녕하세요. <b>chanos</b>입니다. 😉

오늘은 SQLite DB의 병합에 관한 글입니다.

간혹 DataBase(이하 DB) A와 B의 스키마 구조가 같은 경우 데이터 병합을 해야할 경우가 있습니다.

SQLite GUI Tool을 사용하여 두개의 DB를 연결하고 insert select문을 이용하면 간단하게 작업이 가능하지만 코드로 작성할 경우 어떻게 작업을 하는지 알아보겠습니다.

SQLite DB를 연결할 때 하나의 DB Connection String을 갖고 연결하게 됩니다.

그렇기 때문에 두 개의 DB를 연결하기 위해서는 두 개의 Connection을 필요로 합니다.

하지만 이렇게 두 개의 DB를 연결할 경우 insert select문을 작성할 때 하나의 쿼리에 두 개의 DB를 참조해야되는데 방법이 떠오르지가 않았습니다.

```c#
using (var conn1 = new SQLiteConnection("testdb1"))
using (var conn2 = new SQLiteConnection("testdb2"))
{
    // sample table name is 'TEST'
    conn1.Open();
    conn2.Open();

    using (var command = conn1.CreateCommand())
    {
        command.CommandText = "insert into TEST select * from [conn2 연결을 어떻게???]";
        command.ExecuteNonQuery();
    } 
} 
```

이 방법을 해결하기 위해 검색을 통해 자료를 찾아본 결과 SQLite에서 Attach 명령어를 통해 해결할 수 있었습니다.

Attach 명령어 구문 사용은 다음과 같습니다.

```sql
ATTACH '[DATABASE PATH]' AS schema_name
```

이렇게 Attach 명령어를 통해 추가된 DB는 schema_name.table_name을 통해 참조할 수 있게 됩니다.

```sql
ATTACH 'C:\\testdb2' AS source
Insert or Replace into TEST Select * from source.TEST;
```

위 명령구문을 c# 코드로 옮기면 다음과 같이 사용할 수 있습니다.

```c#
using (var conn = new SQLiteConnection(@"Data Source=C:\testdb1;"))
{
    conn.Open();

    using (var command = conn.CreateCommand())
    {
        command.CommandText = @"attach 'C:\testdb2' as source";
        command.ExecuteNonQuery();
    }

    using (var command = conn.CreateCommand())
    {
        command.CommandText = "insert or replace into TEST select * from source.TEST";
        command.ExecuteNonQuery();
    } 
}
```

만약 두 개의 DB에 Password 설정이 되어있는 경우가 있습니다.

이럴 경우 ConnectionString에 Password를 설정하고 Connection 객체를 생성하게 되는데-
```c#
var conn = new SQLiteConnection("Data Source=testdb1;Password=test")
```

기본적으로 위에서 설명한 Attach 명령어 구문을 사용할 경우 연결하려는 DB의 Password는 Connection 객체를 생성했을 때의 Password를 사용하게 됩니다.

하지만 Password가 서로 다른 경우 Attach 명령문에서 key 키워드를 추가해야 합니다.

```sql
ATTACH '[DATABASE PATH]' AS schema_name Key '[PW]'
```

c# 코드로 옮기면 다음과 같습니다.

```sql
#testdb1 - password -> 1234
#testdb2 - password -> 4321
```
```c#
using (var conn = new SQLiteConnection(@"Data Source=C:\testdb1;Password=1234")
{
    conn.Open();

    using (var command = conn.CreateCommand())
    {
        command.CommandText = @"attach 'C:\testdb2' as source key '4321'";
        command.ExecuteNonQuery();
    }

    using (var command = conn.CreateCommand())
    {
        command.CommandText = "insert or replace into TEST select * from source.TEST";
        command.ExecuteNonQuery();
    } 
}
```

---

이렇게 c#에서 서로 다른 SQLite DB를 병합하는 방법을 알아보았습니다.

두 개의 DB를 서로 병합해야 할 경우 오늘 소개해드린 방법을 사용하면 코드에서 큰 리소스 없이 쉽게 처리가 가능할 것 같습니다.

---

## Reference

[SQLite Attach](https://www.sqlite.org/lang_attach.html){:target="_blank"}

[SQLite Attach in c# - stackoverflow](https://stackoverflow.com/questions/4544083/merging-two-sqlite-database-files-c-net){:target="_blank"}