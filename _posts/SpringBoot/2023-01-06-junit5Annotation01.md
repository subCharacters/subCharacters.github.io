# Junit5(Jupiter) Assertions Class 사용법
[참고]<http://www.ne.jp/asahi/hishidama/home/tech/java/junit/5/assertion.html>
### @Test
해당 메소드가 테스트 가능하도록 하는 모듈 역할을 하는 어노테이션.
어노테이션을 붙이면 독자적으로 테스트가 실행이 가능하다.

### @BeforeAll
클래스 단위의 전처리 어노테이션
어노테이션을 실행하기 위해서는 메소드가 static이며 void여야 한다.

### @BeforeEach
메소드 단위의 전처리 어노테이션

### @AfterAll
클래스 단위의 후처리 어노테이션
어노테이션을 실행하기 위해서는 메소드가 static이며 void여야 한다.

### @AfterEach
메소드 단위의 후처리 어노테이션

### @Disabled
테스트 케이스에서 제외 시키는 어노테이션

### @DisplayName
클래스나 메소드의 실행 이름을 따로 지정하는 어노테이션

### @SprinBootTest
통합 테스트를 위한 어노테이션.
Spring boot의 모든 bean을 스캔하고 application context를 생성하여 테스트한다.

### 코드

```kotlin
package cocone.fk.hangeinfo.controller

import org.junit.jupiter.api.*

class JunitAnnotationTest {

    companion object {
        @BeforeAll
        fun beforeAll() {
            println("BeforeAll")
        }

        @AfterAll
        fun afterAll() {
            println("AfterAll")
        }
    }

    @BeforeEach
    fun beforeEach() {
        println("BeforeEach")
    }

    @Test
    @DisplayName("this is unittest")
    fun mainTest() {
        println("Test Case Clear")
    }

    @Test
    fun mainTest2() {
        println("Test Case2 Clear")
    }

    @Test
    @Disabled
    fun mainTest3() {
        println("Test Case3 Clear")
    }

    @AfterEach
    fun afterEach() {
        println("AfterEach")
    }
}
```

실행 결과
![image](..\..\images\junit\junit-annotation02.jpg)

에러없이 실행은 잘 되었지만 BeforeAll과 AfterAll이 실행 되지 않았다.
조건인 static과 void에서 static이 아니였기에 실행이 되지 않았던 것이다.
companion이 자바의 static과 유사하기에 static처럼 사용하는데 companion은 클래스와 함께 로드 되는 것일뿐 static이 아닌 object이므로 메소드를 static으로 만들어줄 필요가 있다.

```kotlin
package cocone.fk.hangeinfo.controller

import org.junit.jupiter.api.*

class JunitAnnotationTest {

    companion object {
        @BeforeAll
        @JvmStatic
        fun beforeAll() {
            println("BeforeAll")
        }

        @AfterAll
        @JvmStatic
        fun afterAll() {
            println("AfterAll")
        }
    }

    @BeforeEach
    fun beforeEach() {
        println("BeforeEach")
    }

    @Test
    @DisplayName("this is unittest")
    fun mainTest() {
        println("Test Case Clear")
    }

    @Test
    fun mainTest2() {
        println("Test Case2 Clear")
    }

    @Test
    @Disabled
    fun mainTest3() {
        println("Test Case3 Clear")
    }

    @AfterEach
    fun afterEach() {
        println("AfterEach")
    }
}
```

실행 결과
![image](..\..\images\junit\junit-annotation02.jpg)
