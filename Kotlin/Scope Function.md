코틀린으로 코드를 짜다보면 **안전한 호출 연산자(?.)** 와 **let** 을 사용할 때가 있었습니다. 값이 `null` 이 아닐 경우 이벤트를 처리하기 때문인데 `let` 의 용도를 정확히 모르고 쓰기 때문에 이렇게 사용하는게 맞고, `let` 말고 다른 스코프 함수를 사용해도 상관이 없는게 아닐까라는 의구심이 들 때가 많습니다.
<br>

그렇기 때문에 이 참에 제대로 알아보고 사용하고자 하는 생각에 Scope Function 내용에 대해 정리해보았습니다.

<br>
<br>
<br>

# Scope Function
`Scope Function` 은 코틀린 표준 라이브러리이며, `this` , `it` 과 같은 `Context Object` 내에서 코드 블록을 실행하기 위한 함수입니다. 람다 식을 이용해 함수를 호출하면 임시적으로 스코프가 설정되기 때문에 스코프 함수라고 불립니다.
<br>

스코프 함수를 사용하면 객체의 이름을 일일이 참조할 필요 없이 객체에 접근하고 핸들링할 수 있다는 점에서 편하다는 장점이 있습니다.  
<br>

스코프 함수는 `let`, `also` , `run` , `apply` , `with` 5가지가 있습니다. 얼핏보면 모두 동일한 역할을 하는 것 같지만 **코드 블록 내에 객체를 사용하는 방법** 과 **반환 값의 결과** 로 차이점을 나눌 수 있습니다.
```kotlin
inline fun <T, R> T.let(block: (T) -> R): R {
    return block(this)
}

inline fun <T, R> T.run(block: T.() -> R): R {
    return block()
}

inline fun <T, R> with(reciever: T, block: T.() -> R): R {
    return reciever.block()
}

inline fun <T> T.apply(block: T.() -> Unit): T {
    block()
    return this
}

inline fun <T> T.also(block: T.() -> Unit): T {
    block(this)
    return this
}
```
코드를 보고 알 수 있듯이 실행 결과가 모두 다릅니다.  
<br>

표로 나타내어서도 함수들을 구분할 수 있습니다.

<P align="center">

|함수|참조 객체|반환 값|확장 함수 여부|
|:---:|:---:|:---:|:---:|
|let|it|람다 결과|O|
|run|this|람다 결과|O|
|run|-|람다 결과|X|
|with|this|람다 결과|X|
|apply|this|수신 객체|O|
|also|it|수신 객체|O|

</p>
그럼 하나씩 알아보도록 하겠습니다.  

<br>
<br>
<br>

# let
`let` 함수는 반환 값을 코드 블록의 수행 결과로 반환하고, `Context Object` 를 인수로 사용하기 떄문에 `it` 을 사용해 프로퍼티에 접근할 수 있습니다.
<br>

`let` 은 **값이 null이 아닐 경우 코드를 실행해야 할 경우**, **변수 범위 내에서 정의하는 경우** 사용합니다.

```kotlin
// Android WebView Settings
webView.settings.let{ setting ->
    setting.javaScriptEnabled = true
    setting.domStroageEnabled = true
    setting.userAgentString = "mobile_app_webview"
}


// nullable check
val len = str?.let{
    println("str is not null")
    it.length
} ?: 0
```

<br>
<br>
<br>

# also
`also` 함수는 반환 값을 수신 받은 객체로 반환하고, Context Object를 인수로 사용하기 때문에 `it` 을 사용해 프로퍼티에 접근할 수 있습니다.
<br>

`also` 는 수신 객체를 사용한 부가적인 작업을 할 때 사용합니다.

```kotlin
val list = mutableListof(1, 2, 3, 4).also{
    println("숫자를 추가하기 전 리스트 : $it")
}.add(5).also{ println(it) }

println(list)
```
위 코드의 list는 true를 반환하는데 **첫 번째** also는 mutableList<Int> 타입의 수신 객체로 받고, **두 번째** also는 add(5)의 Boolean 타입의 수신 객체로 받기 때문에 최종적으로 true를 반환하게 됩니다.  
하지만 이는 정상적인 사용 방법이 아니고 수신 객체 자기 자신을 반환한다는 점을 확인하기 위해 위와 같이 작성했습니다.  
<br>

```kotlin
val list = mutableListOf(1, 2, 3, 4)

list.also { println("숫자를 추가하기 전 리스트 : $it") }.add(5)

println(list)
```
코드를 위와 같이 수정하면 원하는 결과가 나오게 됩니다.

<br>
<br>
<br>

# with
`with` 함수는 코드 블록 수행의 결과를 반환하고, 확장 함수가 아니기 때문에 수신 객체를 인자로 넘깁니다. 하지만 내부 코드에서는 확장 함수로 실행되기 때문에 `this` 를 사용해야 합니다.
<br>

`with` 의 수신 객체는 `non-nullable` 이고, 수신 객체를 사용한 함수 호출을 그룹화하기 위해 사용합니다. 그룹화를 하여 점(.) 연산자의 사용을 줄일 수 있어 편하다는 이점이 있습니다.

```kotlin
val date = Calendar.getInstance()
val format = SimpleDateFormat("yyyy-MM-dd")

with(date){
    add(Calendar.YEAR, 100)
    add(Calendar.MONTH, 2)
    add(Calendar.DAY_OF_MONTH, 10)
}

println(format.format(date.time))
```

<br>
<br>
<br>

# apply
`apply` 함수는 수신 받은 객체를 반환하고, 확장 함수이기 떄문에 `this` 를 사용합니다.
<br>

`apply` 는 수신 객체의 내부 프로퍼티를 변경하거나 객체 생성 시 다양한 프로퍼티를 설정하기 위해 사용합니다. 객체 생성 후 프로퍼티를 설정 할 떄 `apply` 를 사용하면 가독성이 좋아지는 이점이 있습니다.

```kotlin
val person = Person()
person.name = "Kim"
person.age = 21
person.country = "Korea"

// apply 사용
val person = Person().apply{
    name = "Kim"
    age = 21
    country = "Korea"
}
```

<br>
<br>
<br>

# run
`run` 함수는 코드 블록 수행의 결과를 반환하고, 확장 함수이기 때문에 `this` 를 사용합니다.
<br>

`run` 은 수신 객체를 사용한 코드를 수행한 후 결과 값을 반환해야 할 경우 사용합니다.
```kotlin
data class Person(
    var name: String,
    var age: Int,
    var country: String
){
    fun isAdult() = age >= 19
}

val person = Person("Kim", 21, "Korea")
val adult = person.run{
    age = 15
    isAdult()
} // false
```
<br>

`run` 함수는 다른 함수와 다르게 수신 객체 없이 사용이 가능합니다. 하지만 함수 내부에서 수신 객체를 명시해주어야 합니다.
```kotlin
val person = Person("Kim", 21, "Korea")
val adult = run{
    person.age = 15
    person.isAdult()
}
```