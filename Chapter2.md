# Kotlin in Action 2장
코틀린 기초

## 함수와 변수

### Hello World

```kotlin
fun main(args: Array<String>) {
  println("Hello world!")
}
```

- 함수 생성시 fun 키워드 사용
- 파라미터 뒤에 파라미터 타입을 씀
- 최상위 수준에 함수 정의 가능 (자바처럼 꼭 클래스 안에 함수를 넣지 않아도 됨)
- 배열도 일반적인 클래스
- 표준 자바 라이브러리를 간결하게 사용하도록한 wrapper 제공 ex) println
- 세미콜론을 붙이지 않아도 됨

### 함수
```kotlin
fun max(a: Int, b: Int): Int {
  return if (a > b) a else b
}
```

- 코틀린의 if는 결과 반환하는 **식**

### 식이 본문인 함수
```kotlin
fun max(a: Int, b: Int) = if (a > b) a else b
```

- 본문이 중괄호로 둘러싸인 함수 -> 블록이 본문인 함수
- 등호와 식으로 이뤄진 함수 -> 식이 본문인 함수
- 식이 본문인 함수의 경우 반환 타입 추론으로 타입을 명시적으로 적지 않아도 됨

### 변수
```kotlin
val question = "hello world?"
val answer = 42
val foo: Int = 42 // 타입 명시 가능
val yearToCompute = 7.5e6 // Double로 타입 추론됨
```

```kotlin
val answer: Int
answer = 42
```
- 초기화 식을 사용하지 않는 경우 변수 타입을 명시해야함

#### 변경 가능한 변수와 변경 불가능한 변수
- 종류
  - val(value) - 변경 불가능한 참조 immutable (자바의 final)
  - var(variable) - 변경 가능한 참조 mutable (자바의 일반 변수)
- 기본적으로 val을 사용하고 필요할때만 var로 변경
- val 참조 자체는 불변일지라도 참조가 가리키는 객체 내부의 값은 변경될 수 있음

```kotlin
val languages = arrayListOf("Java")
languages.add("Kotlin")
```

- var은 변수 값을 변경할 수 있지만 변수 타입은 고정되어 바뀌지 않음

```kotlin
var answer = 42 // Int
answer = "no answer" // 컴파일 오류
```

#### 문자열 템플릿
```kotlin
val name = "hello"
println("Hello $name")
println("Hi ${name}")
```

#### 클래스와 프로퍼티
```kotlin
class Person(val name: String)
```
- 코틀린은 기본 접근 제한자가 public

```kotlin
class Persion(
  val name: String, // 일기 전용으로 비공개 필드와 공개 게터를 만듦
  var isMarried: Boolean // 쓰기 가능한 프로퍼티로 비공개 필드, 공개 게터, 공개 세터를 만듦
)
```

- 자바에서 `Persion.getName()`과 같은 방식으로 접근 가능

```kotlin
val person = Person("Bob", true)
println(person.name)

person.isMarried = false
println(person.isMarried)
```
- 코틀린에서는 필드를 사용하는 것처럼 사용

### 커스텀 접근자
```kotlin
class Rectangle(val height: Int, val width: Int) {
  val isSquare: Boolean
    get() {
      return height == width
    }
}
```
```kotlin
val rectangle = Rectangle(41, 43)
println(rectangle.isSquare)
```

### 소스코드 구조: 디렉터리와 패키지
```kotlin
package geometry.shapes // 패키지 선언

import java.util.Random // 클래스 import

fun createRandomRectangle(): Rectangle {
  val random = Random()
  return Rectangle(random.nextInt(), random.nextInt())
}
```

```kotlin
package geometry.exmple

import geometry.shapes.createRandomRectangle // 함수 import하기
```

- 함수도 동일한 문법으로 import
- 패키지 이름 뒤에 .*를 추가하면 패키지 안의 모든 선언을 임포트함
- 여러 클래스를 한 파일에 넣을 수 있고 파일 이름도 클래스 이름이랑 달라도 상관 없음
- 대부분 자바와 같이 패키지 별로 디렉터리를 구성하는게 좋음

### enum과 when
when은 자바의 switch를 대치하고 더 강력함

#### enum class
```kotlin
enum class Color {
  RED, ORANGE, YELLOW
}
```
- 프로퍼티와 메소드를 정의할 수 있음

```kotlin
enum class Color(
  val r: Int, val g: Int, val b: Int
) {
  RED(255, 0, 0), ORANGE(25, 165, 0),
  YELLOW(255, 255, 0);
  
  fun rgb() = (r * 256 + g) * 256 + b
}
```

#### when으로 enum 클래스 다루기
- if와 마찬가지로 값을 만들어내는 식

```kotlin
fun getMnemonic(color: Color) =
  when (color) {
    Color.RED -> "Richard"
    Color.ORANGE, Color.YELLO -> "Of" // 콤마로 구분
  }
```

#### when과 임의의 객체를 함께 사용
- 자바의 switch는 상수만 사용가능하지만 when은 임의의 객체를 허용함

```kotlin
fun mix(c1: Color, c2: Color) = 
  when (setOf(c1, c2)) {
    setOf(RED, YELLOW) -> ORANGE
    setOf(YELLOW, BLUE) -> GREEN
    else -> throw Exception("Dirty color")
  }
```

#### 스마트 캐스트
```kotlin
fun eval(e: Expr): Int =
  when (e) {
    is Num -> e.value // Num 타입인지 검사
    is Sum -> eval(e.left) + eval(e.right)
    else -> throw IllegalArgumentException("Unkown expression")
  }
```
- 객체 타입으로 분기 처리가 가능함

## 대상을 이터레이션 while과 for

### while, do while
- 자바와 동일

### 범위와 수열
```kotlin
val oneToTen = 1..10 // 코틀린의 범위는 닫힌 구간임
  
fun fizzBuzz(i: Int) = when {
  i % 15 == 0 -> "FizzBuzz"
  i % 3 == 0 -> "Fizz"
  i % 5 == 0 -> "Buzz"
  else -> "$i"
}

for (i in 1..100)
  println(fizzBuzz(i))
  
for (i in 100 downTo 1 step 2) // 100 부터 1까지 2씩 줄어들며
  println(fizzBuzz(i))
  
val oneToNine = 1 until 10 // 반만 닫힌 범위 == 1..9
```

### 맵에 대한 이터레이션
```kotlin
val map = TreeMap<Chart, String>()

for (c in 'A'..'F') {
  val value = Integer.toBinaryString(c.toInt())
  map[c] = value
}

for ((key, value) in map) {
  println("$key $value")
}
```
- 코틀린에서 맵을 다룰때 get, put 대신 map[key], map[key] = value를 사용 가능

```kotlin
val list = arrayListOf("10", "11", "1001")
for ((index, element) in list.withIndex()) {
  println("$index: $element")
}
```
- 구조 분해 구문을 컬렉션에서 사용할 수 있음

### in으로 컬렉션이나 범위의 원소 검사
```kotlin
fun isLetter(c: Char) = c in 'a'..'z' || c in 'A'..'Z'
fun isNotDigit(c: Char) = c !in '0'..'9'

c in 'a'..'z' // 'a' <= c && c <= 'z'로 변환됨

// when에서 in 사용가능
fun recognize(c: Char) = when (c) {
  in '0'..'9' -> "Digit"
  in 'a'..'z', in 'A'..'Z' -> "Letter"
  else -> "I don't know"
}

"Kotlin" in "Java".."Scala" // 문자열도 비교 가능
"Kotlin" in setOf("Java", "Scala") // 컬렉션에서도 사용 가능
```

## 코틀린의 예외처리
- 자바와 달리 코틀린의 throw는 식이므로 다른 식에 포함 될 수 있음
```kotlin
val percentage =
  if (number in 0..100)
    number
  else
    throw IllegalArgumentException()
```
- try catch finally는 자바와 동일
- 자바와 다르게 체크예외를 따로 구별하지 않음
- try, catch는 식

```kotlin
fun readNumber(reader: BufferedReader) {
  val number = try {
    Integer.parseInt(reader.readLine())
  } catch (e: NumberFormatException) {
    null
  }
  println(number)
}
```

## 요약
- 함수를 정의할때 fun 사용함
- val, var은 각각 불변, 가변 참조
- 문자열 템플릿을 통해 문자열 더하기보다 가독성이 좋고 코드가 간결하짐
- 값 객체 클래스를 간결하게 표시 가능
- if는 코틀린에서 식이며 값을 만듦
- when은 자바의 switch와 비슷하지만 강력함
- 변수 타입을 검사하고 나면 그 변수를 캐스팅 하지 않아도 그 타입처럼 사용이 가능함 (스마트 캐스트)
- 코틀린의 for는 자바의 for보다 편리함 (구조 분해 구문 지원)
- 1..5와 같은 식은 범위를 만들어냄
- 범위 안에 있는지 검사하기 위해 in, !in을 사용
- 예외처리는 자바와 비슷하지만 체크 예외 구분이 없음
