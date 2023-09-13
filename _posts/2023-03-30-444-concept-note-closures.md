---
title: "4.4.4 Concept Note: Closures \n- 실행되는 코드뭉치"
layout: post
current: post
tags:
- swift_lecture
categories:
- Swift Lecture
---

이번 챕터에서는 클로저에 대해 좀 더 깊게 공부해보려고 한다. 클로저는 입문자에게 혼란스러울수 밖에 없는 부분이므로 쉬운 코드부터 차근차근 이해해보도록 하자.

우리가 그동안 다음과 같은 방식으로 변수를 선언했다.

```
let someText: String
```


someText 가 String 타입이라는 것은 우리에게 익숙할 것이다.


이제 클로저를 선언하려고 한다. 여기서 약간 이질감을 느낄수 있는데 함수를 비롯한 모든 클로저도 하나의 객체라는 사실이다.

클로저의 문법을 다시 상기해보자.

```
{ (parameters) -> return type in
	statements
}
```



{ } 안에 파라미터, 리턴타입, 실행문을 넣어주면 되지만 전부 생략 가능하다는 사실을 알고 있을것이다.
그러므로 다음 코드도 물론 클로저이다.

```
{ }
```



이것은 마치 다음과 같은 함수를 선언한것과 같다.

```
func test() {

}
```



test() 는 파라미터도 리턴타입도 실행문도 없는 아무 의미없는 함수이지만 이렇게 선언하는것은 문제되지 않지만 실행해도 아무일도 일어나지 않는다.

클로저도 하나의 객체이므로 다음처럼 변수에 담을 수 있다.
플레이그라운드에 다음 코드를 입력해보자.

```
let someClosure = { }
```


someClosure 의 타입을 확인해 보면 () -> () 이라고 되어 있는데 이것이 someClosure 의 타입이다. 파라미터와 리턴타입이 없는 클로저이므로 이렇게 표시 한것이다.
위 코드는 타입추론이 된것이므로 다음처럼 정확하게 타입까지 선언해 줄수 있다.

```
let someClosure: () -> () = { }
```


이것도 하나의 약속이므로 받아들이고 익숙해지기만 하면 될 것이다. 내부에 간단한 코드를 추가해보자.

```
let someClosure: () -> () = {
    print("test")
}
```


someClosure 를 실행하려면 어떻게 해야 할까? test 함수를 실행하려면 test() 라고 입력하듯이
someClosure() 라고 입력하면 “test” 가 프린트 될 것이다.

이제 간단한 Int 타입 파라미터를 추가해보자.

```
let someClosure: (Int) -> () = { number in
        print("test")
}
```


파라미터가 추가되었으므로 실행하려면 다음처럼 인자를
입력해주어야 한다.

```
someClosure(1)
```


리턴타입도 String 으로 선언해보자.

```
let someClosure: (Int) -> (String) = { number in
    return "number is (number)"
}
```


이제 someClosure 는 리턴값을 갖게 되었으므로 확인해보자.

```
print(someClosure(1))


// "number is 1"
```


이렇게 객체처럼 다룰수 있는 함수를 일급함수(First Order Function)라고 칭한다. 클로저는 일급함수로서 다른 함수의 파라미터로도 전달이 가능하다.

```
let doWithClosure(param:(Int) -> (String)) = {
        print(param(2))
}
```


doWithClosure(param:) 은  (Int) -> (String) 타입의 클로저를 파라미터로 입력받아서 내부에서 해당 클로저에 2를 인자로 입력하여 실행한 결과를 출력하는 함수이다.

```
doWithClosure(param: someClosure(2))

// 결과는 물론 다음과 같다.
// "number is 2"
```


이것이 일급함수로서 클로저의 기능이다.
이번엔 클로저의 다른 측면을 알아보기 위해 다음과 같은 2개의 클래스를 작성해보자.


```

class Sender {

    let job = "Developer"
    let name = "Claude"

    let receiver = Receiver()

    func doSomething() {
        receiver.doWithClosure {
            print(job)
            print(name)
        }
    }
}

class Receiver {

    func doWithClosure(param: () -> ()) {
        param()			// 인자로 받은 클로저를 실행한다.
    }
}

Sender().doSomething()
```


job과 name 이 프린트 될 것이다. 여기서 doWithClosure(param:) 에 job,name 변수를 프린트하는 () -> () 타입의 클로저를 인자로 전달했다.
실제 실행은 doWithClosure(param:) 내부에 param() 에서 되지만 보다시피 Receiver 클래스에는 job, name 변수가 존재하지 않는다. 이것이 클로저의 중요한 특성인데 클로저는 다른 함수에 인자로 전달 가능한 코드뭉치로서 정의되어 있는 스코프에서 호출된 것처럼 실행된다.

![]({{ 'image71.png' | relative_url }})


Sender가 클로저를 다른 메서드에 인자로 전달하면 해당 메서드는 받은 클로저를 실행할 수 있다.
일반적인 메서드가 호출되는것과 다르게 Receiver가 클로저를 실행할 때 클로저 내부의 코드는 Sender의 스코프에 존재하는 것처럼 실행된다.

**바꿔 말하면, 클로저는 레퍼런스 타입으로서 정의시에 주변 환경의 변수를 참조하여 Receiver의 스코프에서 실행되어도 Sender의 상태에 접근 가능하다는 의미이다.**

이번엔 param2 파라미터를 추가하여 모든 작업이 끝나고 가장 마지막에 실행되도록 구현했다.

```
 func doWithClosure(param:  () -> (), param2: () -> ()) {

        param()
        for i in 1...10 {
            print(i)
        }
        param2()
    }
```




이제 doWithClosure 메서드에 두개의 클로저를 인자로 입력해야 한다.

```
  receiver.doWithClosure(param: {
            print(job)
            print(name)

        }, param2: {
            print("finish")
        })

```


param2에 입력된 클로저는 컴플리션 핸들러(Completion Handler)로서 가장 마지막에 실행된다.
comletion 이라고 파라미터의 이름을 지어주면 해당 함수를 사용할 때 구분하기가 쉬울것이다.

```
 func doWithClosure(param:  () -> (), comletion: () -> ()) {

        param()
        for i in 1...10 {
            print(i)
        }
        comletion()
    }
```


컴플리션 핸들러는 일반적으로 마지막 파라미터에 선언되므로 트레일링 클로저가 되어 실제 실행하면 다음처럼 축약형태로 많이 사용된다.

```
 receiver.doWithClosure(param: {
            print(job)
            print(name)

        }) {
            print("finish")
        }
```


이번엔 () -> () 타입 클로저를 담는 빈배열을 선언해 보자.  이전에 String 타입의 빈 배열을 선언할 때 
`[String]()` 이라고 표현했다. 마찬가지로 클로저에 대한 빈배열은 다음과 같다.

'	var closures = [() -> ()]()'


param에 입력되는 클로저를 바로 실행하지 않고 closures 에 추가하려면 다음처럼 코드가 수정되어야 한다.

```
class Receiver {

    var closures = [() -> ()]()

    func doWithClosure(param: @escaping () -> (), completion: () -> ()) {
        closures.append(param)
        for i in 1...10 {
            print(i)
        }
        completion()
    }
}
```


param 을 보면 앞부분에 **@escaping** 이란 낯설은 키워드가 보이는데 이것은 입력되는 클로저가 현재 메서드를 ‘탈출’ 할수 있음을 나타내는 키워드이다.
탈출의 의미를 곱씹어 보자.

메서드의 파라미터는 메서드가 리턴되면 같이 사라지지만 해당 클로저는 언제 실행될지 알 수 없다. 이럴 경우에 메서드가 리턴되더라도 클로저는 독립적인 실행이 가능하도록 해주는 키워드가 **@escaping** 이다. 기본적으로 탈출할 수 없는 nonescaping 상태이므로 지금처럼 escaping 해야 하는 경우 컴파일러가 자동수정되도록 요청한다. 다만 그 의미를 알고 있어야 하기에 앞으로 **@escaping**  키워드가 출현하면 “여기에 입력되는 클로저는 함수가 완전히 종료된 후에 실행되겠구나" 라고 생각하면 된다.

컴플리션 핸들러랑 비슷하게 생각될 수 있는데 컴플리션 핸들러는 이 함수의 모든 작업이 완료되고 마지막에 실행되는 클로저로서 그것이 함수 내부에서 마지막에 실행될 수 도 있고 함수가 종료되고 외부에서 실행될 수도 있는데 이때도 마찬가지로 **@escaping** 키워드가 선언되어야 한다.
