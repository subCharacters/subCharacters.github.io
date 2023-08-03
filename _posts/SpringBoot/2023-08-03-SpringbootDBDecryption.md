---
layout: single
title: "SpringBoot 디비 복호화"
author_profile: true
sidebar:
  nav: "docs"
categories:
  - SpringBoot
tags:
  - SpringBoot
  - LogBack
toc: true
toc_sticky: true
date: 2023-08-03
last_modified_at: 2023-08-03
---

### 개요

---

<span style="font-size:13pt">
암호화된 url을 받아서 복호화 후 디비에 접속하는 방법이다.<br>
더 좋은 방법이 있을 지도 모르겠다.<br>
</span>

### 코드

<span style="font-size:13pt">
방법은 간단하다.<br>
디비 Url을 받아서 커넥트 하는 곳에 복호화 기능을 넣어주면 된다.<br> 
예를 들어 알파벳을 한칸씩 앞으로 이동하는 치환 암호로 암호화 했다고 가정하고 작성해보면
</span>

```kotlin
class Decryptor {

  fun decrypt(arg : String) : String {
      var decryptStr = StringBuilder();
      for (i in str.indices step (1)) {
        decryptStr.append(str[i] - 1)
      }
    return decryptStr.toString()
  }
}
```

<span style="font-size:13pt">
한칸씩 앞으로 밀려 암호화 된 문자열을 하나씩 빼주는 복호화다.<br>
BCD가 왔을 경우 ABC를 반환한다.<br>
그 외 A나 Z일 경우 같은 사소한건 여기서 중요한건 아니니...
</span>

```kotlin
    fun dataSource(@Qualifier("TestDataSourceProperties") dataSourceProperties: DataSourceProperties): DataSource {
        var decryptor = Decryptor()
        dataSourceProperties.url = decryptor.decrypt(dataSourceProperties.url)
        dataSourceProperties.username = decryptor.decrypt(dataSourceProperties.username)
        dataSourceProperties.password = decryptor.decrypt(dataSourceProperties.password)

        return dataSourceProperties.initializeDataSourceBuilder().build()
    }
```

<span style="font-size:13pt">
그리고 해당 디비를 커넥트 하는 곳으로 넘겨주기 직전 해당 복호화 해주고 넘기면 된다.<br>
다른 기사에서 작성한 에러로그를 디비에 넣는 부분에서는 아래와 같이 해주면 된다.<br>
</span>

```kotlin
class ErrorLogDBConnector : DriverManagerConnectionSource() {

    override fun start() {
        var decryptor = Decryptor()
        super.setUrl(decryptor.decrypt(super.getUrl()))
        super.setUser(decryptor.decrypt(super.getUser()))
        super.setPassword(decryptor.decrypt(super.getPassword()))
        super.start()
    }
}
```
