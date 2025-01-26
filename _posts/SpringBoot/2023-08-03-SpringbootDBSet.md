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
last_modified_at: 2025-01-26
---

## 개요

---

<span style="font-size:13pt">
SpringBoot에서 DB를 연결하는 방법이다.<br>
sql은 mapper를 사용하는 방식으로 했다.
</span>

## 1. yml,properties에 정의

<span style="font-size:13pt">
yml 혹은 properies파일에 DB정보를 정의하여 사용하는 방법이다.<br>
spring-boot-starter-jdbc 또는 spring-boot-starter-data-jpa 의존성을 추가하여 사용 할 수 있다.
</span>

### YAML
```yaml
spring:
  # DB연동
  # yml은 이런 방식으로 임포트
  # 설정 파일이 여러개일 경우, 배열 형식으로 가능
  # - db.yml
  # - db2.yml등
  #config:
  #  import: db.yml
  datasource:
    mapper-location: classpath:/mapper/**/*.xml
    username: root
    password: Admin!@34
    driver-class-name: com.mysql.cj.jdbc.Driver
    jdbc-url: jdbc:mysql://localhost:3306/test?useSSL=false&serverTimezone=UTC
#  profiles:
#    include: db
```

### Properties
```ini
spring.datasource.url=jdbc:mysql://localhost:3306/test?useSSL=false&serverTimezone=UTC
spring.datasource.username=root
spring.datasource.password=Admin!@34
spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver
spring.datasource.mapper-location=classpath:/mapper/**/*.xml
```

### java
```java
@Configuration
@MapperScan(value = "com.example.springbootdb_connect.mapper")
public class DataSourceConfig {
    @Value("${spring.datasource.mapper-location}")
    private String mapperPath;

    @Bean
    @ConfigurationProperties(prefix = "spring.datasource")
    public DataSource dataSource() throws Exception {
        return DataSourceBuilder
                .create()
                .build();
    }
    /**
     * MySql과 MyBatis를 연결해주는 객체생성
     *
     * @param dataSource
     * @return
     * @throws Exception
     */
    @Bean
    public SqlSessionFactory sqlSessionFactory(DataSource dataSource) throws Exception {
        PathMatchingResourcePatternResolver resolver = new PathMatchingResourcePatternResolver();
        SqlSessionFactoryBean sqlSessionFactoryBean = new SqlSessionFactoryBean();
        sqlSessionFactoryBean.setDataSource(dataSource);
        sqlSessionFactoryBean.setTypeAliasesPackage("com.example.springbootdb_connect.entity");
        sqlSessionFactoryBean.setMapperLocations(resolver.getResources(mapperPath));

        return sqlSessionFactoryBean.getObject();
    }

    @Bean
    public SqlSessionTemplate sqlSessionTemplate(SqlSessionFactory sqlSessionFactory) {
        return new SqlSessionTemplate(sqlSessionFactory);
    }
}
```

## 2. config에 직접 정의

<span style="font-size:13pt">
java의 config 클래스에 직접 정의하여 사용하는 방법이다.<br>
외부로부터 설정 받아 쓰기가 어렵고 설정이 바뀌면 코드 수정도 해야 하기에 개인적으로 그닥...
</span>

### java
```java
@Configuration
@MapperScan(value = "com.example.springbootdb_connect.mapper")
public class DataSourceConfig {

    @Value("classpath:/mapper/**/*.xml")
    private String mapperPath;

    @Bean
    public DataSource dataSource() throws Exception {
        DriverManagerDataSource dataSource = new DriverManagerDataSource();
        dataSource.setDriverClassName("com.mysql.cj.jdbc.Driver");
        dataSource.setUrl("jdbc:mysql://localhost:3306/test?useSSL=false&serverTimezone=UTC");
        dataSource.setUsername("root");
        dataSource.setPassword("Admin!@34");
        return dataSource;
    }

    /**
     * MySql과 MyBatis를 연결해주는 객체생성
     *
     * @param dataSource
     * @return
     * @throws Exception
     */
    @Bean
    public SqlSessionFactory sqlSessionFactory(DataSource dataSource) throws Exception {
        PathMatchingResourcePatternResolver resolver = new PathMatchingResourcePatternResolver();
        SqlSessionFactoryBean sqlSessionFactoryBean = new SqlSessionFactoryBean();
        sqlSessionFactoryBean.setDataSource(dataSource);
        sqlSessionFactoryBean.setTypeAliasesPackage("com.example.springbootdb_connect.entity");
        sqlSessionFactoryBean.setMapperLocations(resolver.getResources(mapperPath));

        return sqlSessionFactoryBean.getObject();
    }

    @Bean
    public SqlSessionTemplate sqlSessionTemplate(SqlSessionFactory sqlSessionFactory) {
        return new SqlSessionTemplate(sqlSessionFactory);
    }
}
```

## 3. 섞어서 쓰기

<span style="font-size:13pt">
두 가지 방식을 섞는 방식이다.
</span>

### YAML
```yaml
spring:
  datasource:
    mapper-location: classpath:/mapper/**/*.xml
    username: root
    password: Admin!@34
    driver-class-name: com.mysql.cj.jdbc.Driver
    jdbc-url: jdbc:mysql://localhost:3306/test?useSSL=false&serverTimezone=UTC
```

### java
```java
@Configuration
@MapperScan(value = "com.example.springbootdb_connect.mapper")
public class DataSourceConfig {

    @Value("${spring.datasource.mapper-location}")
    private String mapperPath;

    @Value("${spring.datasource.url}")
    private String url;

    @Value("${spring.datasource.username}")
    private String username;

    @Value("${spring.datasource.password}")
    private String password;

    @Value("${spring.datasource.driver-class-name}")
    private String driverClassName;

    @Bean
    public DataSource dataSource() throws Exception {
        DriverManagerDataSource dataSource = new DriverManagerDataSource();
        dataSource.setDriverClassName(driverClassName);
        dataSource.setUrl(url);
        dataSource.setUsername(username);
        dataSource.setPassword(password);
        return dataSource;
    }

    /**
     * MySql과 MyBatis를 연결해주는 객체생성
     *
     * @param dataSource
     * @return
     * @throws Exception
     */
    @Bean
    public SqlSessionFactory sqlSessionFactory(DataSource dataSource) throws Exception {
        PathMatchingResourcePatternResolver resolver = new PathMatchingResourcePatternResolver();
        SqlSessionFactoryBean sqlSessionFactoryBean = new SqlSessionFactoryBean();
        sqlSessionFactoryBean.setDataSource(dataSource);
        sqlSessionFactoryBean.setTypeAliasesPackage("com.example.springbootdb_connect.entity");
        sqlSessionFactoryBean.setMapperLocations(resolver.getResources(mapperPath));

        return sqlSessionFactoryBean.getObject();
    }

    @Bean
    public SqlSessionTemplate sqlSessionTemplate(SqlSessionFactory sqlSessionFactory) {
        return new SqlSessionTemplate(sqlSessionFactory);
    }
}
```