---
layout: single
title: "Gradle에서 리소스 다운로드"
author_profile: true
sidebar:
  nav: "docs"
categories:
  - SpringBoot
tags:
  - SpringBoot
  - Gradle
toc: true
toc_sticky: true
date: 2023-07-31
last_modified_at: 2023-07-31
---

### 개요

---

<span style="font-size:13pt">
Gradle Task에서 외부 리소스를 다운로드 하는 방법이다.<br>
파일 서버여야 하며, S3인 경우 서버와 마운트가 필요하다.<br>
일하면서 만들었는데 써먹을때가 있지 않을까 싶어서 메모.<br>
</span>

[참고사이트1](https://michelkraemer.com/recipes-for-gradle-download/)
[참고사이트2](https://github.com/michel-kraemer/gradle-download-task)

### 코드

```groovy
buildscript {
    ext {
        springBootVersion = 'springBootVersion'
    }
    repositories {
        mavenCentral()
        maven {
            url "https://plugins.gradle.org/m2/"
        }
    }
    dependencies {
        classpath "org.springframework.boot:spring-boot-gradle-plugin:$springBootVersion"
        classpath "de.undercouch:gradle-download-task:5.3.1" // for download task
        classpath "org.apache.ivy:ivy:2.5.0" // for download task
        classpath "org.codehaus.groovy:groovy-xml:3.0.9"
    }
}

plugins {
    id "de.undercouch.download" version "5.3.1" // for download task
}
apply plugin: 'de.undercouch.download' // for download task

apply plugin: 'java'
apply plugin: 'eclipse'
apply plugin: 'idea'
apply plugin: 'org.springframework.boot'
apply plugin: 'io.spring.dependency-management'
apply plugin: 'jar'

sourceCompatibility = 1.8
targetCompatibility = 1.8

processResources.destinationDir = compileJava.destinationDir
compileJava.dependsOn processResources

...
중략
...

task downloadResource {
    doLast {
        def file = file('src/main/resources/resourceDownloadLinks.xml')
        def urls = new XmlParser().parse(file)

        for (url in urls) {
            downloadFiles(url.@dir, url.text(), url.@base, url.@jarname, url.@kind)
        }
    }
}

import org.springframework.util.StringUtils

def downloadFiles(dir, url, base, jarname, kind) {
    def user = "user"
    def pwd = "pwd"
    def phase = System.getProperty("phase")

    file(dir).mkdir()

    if ("resource" == kind) {
        def urlLister = new org.apache.ivy.util.url.ApacheURLLister()
        def fileList = urlLister.listAll(new URL(url))

        for (fileUrl in fileList) {
            def fileName = fileUrl.toString()
            if (fileName.endsWith('/')) {
                downloadFiles(dir, fileName, base, jarname ,kind)
            } else {
                def dirPath = ""
                if (!StringUtils.isEmpty(base)) {
                    if (fileName.indexOf(base) != -1) {
                        def lastIndex = fileName.lastIndexOf('/')
                        def baseIndex = fileName.indexOf(base)
                        dirPath = fileName.substring(baseIndex + base.length(), lastIndex+1)
                    }
                }
                file(dir + dirPath).mkdirs()

                download.run {
                    src([fileName])
                    dest dir + dirPath
                    overwrite true
                    onlyIfModified false
                }
            }
        }
    } else if ("repos" == kind) {
        def downlodPath = url + phase + "/" + dir + jarname + ".jar"
        download.run {
            src ([
                    downlodPath
            ])
            username 'username'
            password 'password'
            dest dir
            overwrite true
            onlyIfModified false
        }
    } else if ("repos2" == kind) {
        def encodedCredentials = Base64.getEncoder().encodeToString("$user:$pwd".getBytes('UTF-8'))
        def authHeader = "Basic $encodedCredentials"
        def urlStr = url
        def directoryAlphabet = "A"
        switch (phase) {
            case "beta" :
                directoryAlphabet = "B"
                break;
            case "release" :
                directoryAlphabet = "R"
                break;
            default:
                directoryAlphabet = "A"
        }

        URL repos2Url = new URL(urlStr)
        def loginConnection = repos2Url.openConnection() as HttpURLConnection
        loginConnection.setRequestProperty('Authorization', authHeader)

        def subUrls = []
        loginConnection.with {
            inputStream.eachLine { line ->
                def matcher = line =~ /<a\s+.*?href=['"](.*?)['"]/
                matcher.each {
                    String subUrl = it[1].trim()
                    if (subUrl.startsWith("http")) {
                        subUrls.add(subUrl)
                    } else {
                        subUrls.add(urlStr + subUrl)
                    }
                }
            }
        }

        def latestUrl = subUrls.max { subUrl ->
            def match = subUrl =~ /$directoryAlphabet(\d{12})\//
            if (match) {
                def numberString = match[0][1]
                return numberString.toLong()
            } else {
                return 0
            }
        }

        def jarFilePhase = phase == "release" ? "": "." + phase;
        def jarFileURL = latestUrl + jarname + jarFilePhase + ".jar"

        download.run {
            src ([
                    jarFileURL
            ])
            username 'username'
            password 'password'
            dest 'libs/'
            overwrite true
            onlyIfModified false
        }
    }
}

...
중략
...

dependencies {
    compile('org.springframework.boot:spring-boot-starter-web')

    ...
    중략
    ...

    // resource 다운로드 필요 파일
    compile group: 'org.apache.ivy', name: 'ivy', version: '2.5.0'
    implementation 'org.codehaus.groovy:groovy-xml:3.0.9'

    ...
    중략
    ...

}

```

<span style="font-size:13pt">
xml파일은 이런식으로 작성했다. <br>
실행 스크립트는 gradlew downloadResource -Dphase=alpha 이런식 <br>
</span>
```xml
<?xml version="1.0" encoding="UTF-8" ?>

<urls>
<!-- dir = 다운로드 받을 패스 -->
<!-- kind = 다운로드 파일 구분. 구성 당시 파일 서버가 3군데이고 구성이 제각각이라 나눴다. -->
<!-- base = 리소스 파일 디렉토리 구성이 복잡해서 편의성으로 위해 추가 했다. 필요에 따라 없애도 될듯. -->
<!-- jarname = 다운로드 받을 파일의 이름 -->
<!-- 태크 안에는 파일서버의 url을 넣으면 된다. -->
    <url dir="src/main/resources/" kind="resource" base="conf/">http://serverUrlPath/</url>
    <url dir="src/main/webapp/common/" kind="resource" base="common/">http://serverUrlPath/</url>

    <url dir="libs/" kind="repos" jarname="lucy-db-1.6">http://serverUrlPath/</url>

    <url dir="libs/" kind="repos2" jarname="hanlib-friend">http://serverUrlPath/</url>

</urls>
```
