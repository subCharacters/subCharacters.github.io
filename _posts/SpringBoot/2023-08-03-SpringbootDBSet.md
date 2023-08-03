---
layout: single
title: "SpringBoot DB연결"
author_profile: true
sidebar:
  nav: "docs"
categories:
  - SpringBoot
tags:
  - SpringBoot
  - DB
toc: true
toc_sticky: true
date: 2023-08-03
last_modified_at: 2023-08-03
---

### 개요

---

<span style="font-size:13pt">
SpringBoot에서 DB를 연결하는 방법이다.<br>
3가지 정도 방법이 있는데 하나씩 추가 할 예정.
</span>

### 방법 1

<span style="font-size:13pt">
config 클래스를 정의해서 디비와 연결하는 법<br>
sql은 mapper를 사용하는 방식으로 했다.
</span>

```kotlin
@Configuration
@MapperScan(
    basePackages = ["Repository가 있는 패키지 루트"],
    sqlSessionTemplateRef = "TestSqlSessionTemplate" // sql session의 이름
)
class DbConfig {
    val log: Logger = LoggerFactory.getLogger(DbConfig::class.java)

    @Bean("TestDataSourceProperties")
    @ConfigurationProperties(prefix = "datasource.database.mysql-test") // yml에 정의한 값을 가져온다.
    fun dataSourceProperties(): DataSourceProperties {
        log.info("init db datasource.")

        return DataSourceProperties()
    }

    @Bean("TestdbDataSource") // 빈 이름을 지정
    // Qualifier로 빈을 이름으로 지정해서 가져온다.
    fun dataSource(@Qualifier("TestDataSourceProperties") dataSourceProperties: DataSourceProperties): DataSource {
        // url, username, password를 가지고 디비에 접속하는 로직인데
        // 이 부분은 없어도 된다.
        // 나중에 암호화된 디비 정보를 복호화 할때 다룰것.
        var decryptor = Decryptor()
        dataSourceProperties.url = decryptor.decrypt(dataSourceProperties.url)
        dataSourceProperties.username = decryptor.decrypt(dataSourceProperties.username)
        dataSourceProperties.password = decryptor.decrypt(dataSourceProperties.password)

        return dataSourceProperties.initializeDataSourceBuilder().build()
    }

    @Bean("TestSqlSessionFactory")
    fun sqlSessionFactory(@Qualifier("TestdbDataSource") dataSource: DataSource): SqlSessionFactory? {
        val mapperXmlPath = "classpath:sql/mapper/oracle/test.xml" // sql xml이 있는 루트와 파일명을 기재
        val bean = SqlSessionFactoryBean()
        bean.setDataSource(dataSource)
        bean.setTypeAliasesPackage("test.domain") // Entity클래스가 있는 곳의 루트
        bean.setMapperLocations(PathMatchingResourcePatternResolver().getResource(mapperXmlPath))
        return bean.`object`
    }

    @Bean("TestSqlSessionTemplate")
    fun sqlSessionTemplate(@Qualifier("TestSqlSessionFactory") sqlSessionFactory: SqlSessionFactory): SqlSessionTemplate {
        return SqlSessionTemplate(sqlSessionFactory)
    }
}
```

<span style="font-size:13pt">
이후 시간이 나면 추가로 작성.. 과연..
</span>

### 추가

<span style="font-size:13pt">
환경별로 db정보를 나눠서 보관하는 법.<br>
application-database_환경.yml ex) application-database_dev.yml과 같이 yml파일을 하나 만들고 디비 정보를 입력한다.
</span>

```yml
datasource:
  database:
    oracle:
      driver-class-name: oracle.jdbc.OracleDriver
      url: jdbc:oracle:thin:@주소
      username: 유저명
      password: 패스워드
```

<span style="font-size:13pt">
application-dev.yml 와 같은 application.yml에 가서 include한다.
</span>

```yml
spring:
  profiles:
    active: dev
    include:
      - database_alpha
```
