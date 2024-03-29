---
title: "3.1.1 Concept Note  - \nClass vs Struct in Swift"
categories:
- Swift Lecture
layout: post
---

이번 챕터부터 보통 구조체라고 번역되는 스트럭트(Struct)를 처음 사용할 것이다. 클래스와 스트럭처는 상당히 유사하지만 가장 큰 차이점은 클래스는 레퍼런스 타입(reference type), 스트럭처는 밸류 타입(value type) 이라는 것이다.
단편적인 예로 이해하기 위해 다음 코드를 플레이그라운드에 입력해보자.

``` 
class MyClass {
   var str = "I'm a Class"
}

let classA = MyClass()
let classB = classA
classB.str = "Hello"
print(classA.str)
```


classA 를 classB 에 할당하고 classB.str 에 다른 문자열을 대입하고 classA.str 을 프린트 해보면 “Hello” 가 출력된다.
나는 분명히 classB 에 할당했는데 classA 까지 데이터가 바뀌었는데 이것이 레퍼런스 타입이다. 

> reference  (정보를 얻기 위해) 찾아봄, 참고, 참조
> 
이때 classA는 MyClass 인스턴스를 ‘참조’ 한다. 

쉬운 비유를 들어, A라는 학생이 선생님에게 reference 의 뜻을 물어보았는데 선생님은 답을 알려주는 대신 ‘321페이지 10번째줄' 이라고 적힌 쪽지를 건네주었다. 사전을 찾아보니 ‘참고'라고 써있다. 선생님은 ‘참고' 라는 의미를 알려주는 대신 의미가 담겨있는 ‘위치'를 전달해 준 것이다. 

이번엔 B학생이 A학생에게 같은 뜻을 물어보았는데 마찬가지로  ‘321페이지 10번째줄’ 이 적힌 쪽지를 건네주었다. 두 학생은 reference 의 의미를 알고자 할때 같은 사전의 같은 위치를 서로 참조한다. 그런데 B 학생이 의미를 바꾸고 싶어서 사전에 ‘참고' 대신 ‘참조' 라고 바꾸어 놓았다.
이제 A학생도 reference 의 의미를 찾아보면 자동적으로 ‘참조' 라고 인식한다. 위에서 classA 의 데이터가 바뀐것은 이런 원리이다. 
여기서 classA, classB 상수는 MyClass() 인스턴스 하나를 같이 참조하고 있다. 

이번엔 스트럭트를 보자.



```
struct MyStruct {
   var str = "I'm a Struct"
}

var structA = MyStruct()
var structB = structA
structB.str = "Hello"
print(structA.str)
```


이번엔 structA.str 은 “I’m a Struct” 를 출력한다.

마찬가지 예를 들어, A학생이 선생님에게 ‘value’ 의 뜻을 물어보니 선생님은 쪽지에 ‘가치' 라고 적어서 주었다. 이제 A학생은 사전을 보는게 아니라 그 쪽지를 본다. 그리고 B학생에게도 다른 쪽지에 ‘가치'라고 써서 알려주었다. 두학생은 같은 의미로 인식하지만 보고 있는 쪽지가 다르다. 이번엔 B학생이 ‘가치' 대신 ‘값' 이라고 고쳐 적었다. 그렇지만 A학생은 여전히 ‘가치'라고 적힌 쪽지를 들고 있으므로 의미가 변하지 않는다. 

이것이 레퍼런스 타입과 밸류타입의 차이점이다.
클래스는 데이터가 담겨 있는 위치를 전달하여 그 데이터를 공유하지만 스트럭트는 데이터를 복사한다.
이렇게보면 스트럭트는 클래스에 비해 꽤나 비효율적으로 보이지만 내부적으로 **Copy-on-write (COW)** 라는 기술이 적용되어
퍼포먼스를 극대화 한다.

> Copy-on-write (COW): 값이 수정되는 경우에만 실제 복사하여 메모리에 할당하는 기술

위 코드를 다시보면,
```
struct MyStruct {
   var str = "I'm a Struct"
}

var structA = MyStruct() //MyStruct 인스턴스 생성
var structB = structA    //structB에 structA 인스턴스를 할당하지만 structB는 structA를 참조하여 같은 메모리공간을 공유한다.
structB.str = "Hello"   //structB가 실제 복사되어 다른 메모리공간을 점유한다
print(structA.str)
```



그런데 코드를 다시보면 석연치 않은 점이 발견된다.
클래스는 ‘let’으로 선언해도 값이 바뀌지만 스트럭처는 오류를 일으킨다. 이건 왜 그럴까?

‘let’ 으로 선언한다는 것은 수정하지 못하도록 마치 볼펜으로 쓰는 것과 비슷하다.
반면에 ‘var’ 는 언제든 수정할 수 있도록 연필로 쓰는것이라고 생각해보자.

클래스는 수정하지 못하도록 ‘let’ 으로 선언한 프로퍼티에 할당했는데 이것은 쪽지에 ‘321페이지 10번째 라인' 을 볼펜으로 적은 것이다.
그러므로 이 위치 는 수정할 수 없지만 그 위치가 가리키는 데이터는 ‘var’ 로 선언되어 있으므로 수정이 가능하다. 

그러나 스트럭처는 곧바로 값을 복사하기 때문에 ‘let’ 으로 선언한다면 쪽지에 ‘가치' 라고 볼펜으로 적는것과 비슷하므로 값을 수정할 수 없다.

좀 더 나아가 기존 코드에 프로퍼티를 수정하는 메서드를 추가했다.

```
class MyClass {
   var str = "I'm a Class"
   func change() {
       self.str = "Hello"
   }
}

struct MyStruct {
   var str = "I'm a Struct"
   func change() {
       self.str = "Hello "
   }
}
```


이 경우 클래스는 문제가 없지만 스트럭처는 컴파일 자체가 안되는데 마찬가지로 레퍼런스 타입과 밸류타입의 차이에 의해 발생하는 문제이다. 클래스나 스트럭처 내부에서 ‘self’ 는 해당 인스턴스를 의미한다. 그러므로 스트럭처 내부에서 self 또한 값이 복사 되므로 프로퍼티를 읽을 수는 있지만 수정자체가 원칙적으로 안되는 것이다. 그러므로 메서드로 프로퍼티 값을 바꾸고 싶다면 우회적으로 새로운 인스턴스를 생성하여 덮어쓰는 방식을 취해야 하는데 이것이 무척 번거롭고 가독성도 떨어지기 때문에 스위프트에서 방법을 마련해 놓은것이 ‘mutating’ 이라는 키워드이다.

```
// 메서드 앞에 'mutating' 을 선언해준다.
   mutating func change() {
       self.str = "Hello "
   }
```


이제 메서드에서 내부 프로퍼티 수정이 가능해졌다. 엄밀히 말해 수정이라보다 내부적으로 ‘덮어쓰기' 를 한다고 생각하면 된다. 어짜피 컴파일러가 알아서 해당 키워드를 추가하도록 유도하겠지만 깔끔한 이해를 위해 추가설명해 보았다.

이러한 근본적인 차이점으로 인해 클래스가 스트럭처와 구분되는 가장 큰 특징은 ‘상속’이 가능하다는 것이다. 
프로그래밍에서 상속은 상당히 큰 편리함을 제공해준다. 

예를 들어, 강아지,고양이,호랑이 등에 대한 클래스를 설계한다고 해보자. 다시한번 강조하지만 클래스를 설계하는것은 속성과 행위를 규정하는것이다. 모두 각각의 클래스를 따로 설계할 수 있지만 이들은 다리가 4개라든지 털이 있다는 속성과 새끼를 낳고 먹이를 먹는 등의 공통되는 행위가 있다. 그것을 animal 이라는 클래스로 만들고 각각의 클래스에서 상속받기만 하면 따로 코드를 작성할 필요가 없이 속성과 행위를 그대로 사용할 수 있다. 

이때 상위의 클래스를 수퍼클래스, 하위의 클래스를 서브클래스라고 하며 서브클래스는 수퍼클래스를 참조하여 내부의 프로퍼티와 메서드를 자유롭게(마치 원래 구현해 놓은것처럼) 사용할 수 있다. 

공통되는 프로퍼티와 메소드를 물려받았어도 경우에 따라 서브클래스에서 다르게 쓰고 싶을수 있는데 이때 사용하는 키워드가 오버라이드(override) 이다. 오버라이드를 하면 이름은 같지만 다르게 구현해야할 경우에 용이하다. 

> override  (직권을 이용하여 결정·명령 등을) 기각[무시]하다


수퍼클래스의 기능도 사용하면서 다르게 정의하고 싶다면 ‘super’ 키워드를 사용하면 된다. 


앞으로 다음과 같은 코드를 자주 보게 될 것이다. 

```
class ViewController : UIViewController {
....
```


이것은 애플에서 만들어 놓은 UIViewController 를 상속받는 코드이다. 
우리는 상속을 통해 뷰컨트롤러의 방대한 기능을 처음부터 구현하지 않고 자유롭게 사용할 수 있다. 

아래 익숙한 코드가 바로 viewDidLoad 메서드를 오버라이드한 것이다. 
viewDidLoad 메서드는 UIViewController 에 이미 구현되어 있지만 다르게 구현하기 위해 오버라이드하고 수퍼클래스의 기능을 그대로 실행하기위해 super 라는 키워드가 사용되었다. 

```
override viewDidLoad() {
  super.viewDidLoad()
}
```


앞으로 오버라이드 키워드가 나오거나 사용해야 할 경우 다음을 기억하자. 
“수퍼클래스의 그 메서드를 다르게 사용하려고 한다”

그럼 스트럭트는 상속이 안되기 때문에 모두 따로 만들어야 할까?
바로 그점을 보완하는것이 프로토콜이다. 상속을 받게되면 수퍼클래스의 모든 프로퍼티와
메서드를 물려받게 되는 비효율성과 상속이 많아지면 구조파악이 어렵다는 단점이 존재한다. 
스트럭처에 프로토콜을 사용하면 이러한 단점을 보완하여 기능별로 상속과 동일한 효과를 
낼수 있고 복수로 채택이 가능하며 구조가 단순해지는 장점이 있다. 
그래서 애플은 오히려 클래스보다 스트럭처의 사용을 권장하고 이미 스위프트를 
스트럭처와 프로토콜을 적극적으로 사용하는 프로토콜 지향언어(Protocol Oriented Language) 라고 소개한바 있다. 
우리에게 익숙한 String, Int 등 외에도 애플에서 기본적으로 제공하는 대부분의 타입은 스트럭트로 만들어져 있다. 



|   |  Class |  Struct |
| -------- | -------- |  ------ |
| Memory     |  힙에 저장되고 레퍼런스 카운트로 개발자가 관리(느리다)  | 스택에 저장되고 컴파일러가 관리(빠르다)
| 상속     |  가능 | 불가능
| 타입 | 레퍼런스 타입: 인스턴스 생성시 레퍼런스로 메모리에 저장 | 밸류타입: 인스턴스 생성시 값 자체가 메모리에 저장
|Mutability| let 으로 선언해도 내부 프로퍼티 변경가능 | let으로 선언하면 내부 프로퍼티 변경불가
| Initialization| designated initializer + optional initializers |  memberwise initializer 
| Default initialization| 없음 | 있음
|Copy-on-write| 사용안함 | 사용함
