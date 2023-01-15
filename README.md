# Swift-OOP&SOLID

<br/>

## Table Of Content

* OOP & Advantages
* OOP 4 features
* SOLID
    * Single Resposibility Principle
    * Open Closed Principle
    * Liskov Substitution Principle
    * Interface Segregation Principle
    * Dependency Inversion Principle

<br/><br/>

### OOP & Advantages

* OOP?
    * 프로그래밍 에서 필요한 데이터를 추상화시켜, 상태와 행위를 가진 객체를 만들고 그 객체들 간의 유기적인 상호작용을 통해 로직을 구성하는 프로그래밍 방법
* Advantages?
    * 코드의 재사용
        * 다른 클래스를 가져와 이용 할 수 있고 상속을 통해 확장해서 사용 할 수 있다.
    * 유지보수 용이
        * 절차지향 프로그래밍에서 코드를 수정해야 할 때 일일이 찾아 수정 해야하는 반면, 객체지향 프로그래밍 에서는 수정 해야 할 부분이 클래스 내부에 멤버 변수 혹은 매서드로 존재 하기때문에 해당 부분만 수정하면 된다

<br/><br/>

### OOP 4 features

#### *추상화*

* 공통의 속성이나 기능을 묶는 것(어느 자료형으로 묶어도 상관없음)

```swift
protocol Human {
    var name: String { get }
    var age: Int { get }
    var weight: Double { get }
    var height: Double { get }
}
```

<br/>

#### *캡슐화*

* 기능구현을 외부에 감춘다(접근제어자를 통한 정보은닉)
* 캡슐화를 통해 기능을 사용하는 코드에 영향을 주지 않고, 내부에서만 기능을 변경 할 수 있는 유연함이 생김
* 이로써, 유지보수 비용을 낮출 수 있음



<u>*Before*</u>

* 배터리 특정 해주는 기능 노출
* 추가 요구사항(배터리 측정 연산 변경 등) 있을시, 외부에 기능 구현 코드도 수정하는 비용이 들 수 있음.

```swift
class Macbook {
    var battery: Int
    
    init(battery: Int) {
        self.battery = battery
    }
    
    func checkAppleCare() -> Bool {
        
        let calculator = (100 / 100 + 50 - 20 + 30)
        
        let calculatedBattery = calculator * battery
        
        if calculatedBattery > 90 {
            return true
        } else {
            return false
        }
    }
}

let myMacBook = Macbook(battery: 78)
myMacBook.checkAppleCare()
```



<u>*After*</u>

* access control을 이용하여 외부에서는 기능을 알 수 없게 + 기능 훼손 방지
* 추가적인 요구사항에도 외부 코드 수정없이 내부 기능만 수정 
* **캡슐화 된 기능의 return 값을 가져와서 판단 안하는게 핵심** (가져와서 판단을 하게 되면 추가 요구사항에 또 유지보수 비용 비싸짐)

```swift
class Macbook {
    var battery: Int
    
    init(battery: Int) {
        self.battery = battery
    }
    
    private func getBattery() -> Bool {
        let calculator = (100 / 100 + 50 - 20 + 30)
        
        let calculatedBattery = calculator * battery
        
        if calculatedBattery > 90 {
            return true
        } else {
            return false
        }
    }
    
    func checkAppleCare() -> Bool {
        return getBattery()
    }
    
}

let myMacBook = Macbook(battery: 78)
myMacBook.checkAppleCare()
```



<br/>

#### *상속성*

* 부모 클래스의 속성과 기능을 이어받아 사용 할 수 있게 한다
* 즉 기능과 속성이 재사용 되게끔 확장이 일어나야한다
* 이로써, 코드의 중복을 방지, 재사용성을 늘린다

```swift
class Human {
    var name: String
    var age: Int
    var height: Int
    var weight: Int
    
    init(name: String, age: Int, height: Int, weight: Int) {
        self.name = name
        self.age = age
        self.height = height
        self.weight = weight
    }
	  func greeting() {
      // code ..
    }
}

class Asian: Human {
  
}
class Korean: Asian {
  	
}
```

<br/>

#### *다형성*

* 하나의 매소드나 클래스가 있을때, 이것들이 다양한 방법으로 동작하는 것 (== 다양한 형채로 나타낼수 있다)
* 여러타입을 위한 단일 인터페이스를 말하는 것
* 매소드 에서는 오버로딩(overloading)과 오버라이딩(overriding)
* 다형성을 이용하여, 모듈들이 추상체에 의존 하도록 설계 할 수 있음 

```swift
protocol Animal {}

class Cat: Animal {}
class Dog: Animal {}
class Hamster: Animal {}

var myCat = Cat()
var myDog = Dog()
var myHamster = Hamster()

var myPet: [Animal] = [myCat, myDog, myHamster]
```





<br/><br/>





### SOLID

#### *Single Resposibility Principle*



* 하나의 클래스의 하나의 책임
* 많은 역할을 한 class가 하게 된다면, 매서드 끼리 강한 결합 발생 가능성이 있음
* 따라서, class 를 수정 하게 될시, 가독성 저하 및 유지보수 비용 증가
* 역할을 나누게 되면 각 class 별로 역할을 가지게 되서 응집도는 올라가고 결합도는 낮아짐



<u>*Before*</u>

* `ProductionUpdateService` 는 가격을 update 해야 하는 역할을 가지고 있다
* `checkValidatePrice`는 상품의 가격을 변경 시키는 유효성 로직으로 `Production` 의 책임(협력 관계에 있는 기능들은 한곳에 배치해도 상관없)

```swift
class Production {
    static let minmumPrice = 10000
    
    private let name: String
    private var price: Int
    
    init(name: String, price: Int) {
        self.name = name
        self.price = price
    }
    
    func updatePrice(price: Int) {
        self.price = price
    }
}
```

```swift
final class ProductionUpdateService {
    
    let production: Production?
    
    init(production: Production) {
        self.production = production
    }
    
    func update(price: Int) {
        if checkValidatePrice(price: price) {
            production?.updatePrice(price: price)
        } else {
            print("최소 가격은 \(Production.minmumPrice)원 입니다. ")
        }
    }
    
    private func checkValidatePrice(price: Int) -> Bool {
        if price < 10000 {
            return false
        } else {
            return true
        }
    }
}
```

```swift
let bag = Production(name: "구찌가방", price: 10001)
let service = ProductionUpdateService(production: bag)
service.update(price: 2000) // "최소 가격은 10000원 입니다."
```





<u>*After*</u>

* `checkValidatePrice` 책임을 `Production`으로 분리함으로써 `ProductionUpdateService`는 단일 책임만 가지도록 설계

```swift
class Production {
    static let minmumPrice = 10000
    
    private let name: String
    private var price: Int
    
    init(name: String, price: Int) {
        self.name = name
        self.price = price
    }
    
    func updatePrice(price: Int) {
        if checkValidatePrice(price: price) {
            self.price = price
            print("\(price)원 으로 변경 완료")
        } else {
            print("최소 가격은 \(Production.minmumPrice)원 입니다. ")
        }
    }
    
    private func checkValidatePrice(price: Int) -> Bool {
        if price < 10000 {
            return false
        } else {
            return true
        }
    }
}
```



```swift
final class ProductionUpdateService {
    
    let production: Production?
    
    init(production: Production) {
        self.production = production
    }
    
    func update(price: Int) {
        production?.updatePrice(price: price)
    }
}

let bag = Production(name: "구찌가방", price: 10001)
let service = ProductionUpdateService(production: bag)
service.update(price: 20000000) 
```

<br/>

#### *Open Closed Principle*

* 확장에는 열려있고, 변경에는 닫혀 있게 해야한다.
* 즉 추가되는 요구사항이 생기더라도, 기존코드는 유지 한채로 확장이 가능해야한다.
* OCP를 잘 하기 위해서는 변하지 않는 본질적인 특징(Interface)을 과 특정 객체만의 식별되는 특징을 잘 구분 해야 한다

<u>*before*</u>

* 추가 되는 동물들이 500마리 면 if else 문이 감당 할 수 없게 늘어나는 코드 

```swift
enum AnimalType {
    case cat
    case dog
    case tigger
}

class AnimalSoundService {
    var type: AnimalType
    
    init(animalType: AnimalType) {
        self.type = animalType
    }
    func hello() {
        if type == .cat {
            print("야옹")
        } else if type == .dog {
            print("멍멍")
        } else {
            print("어흥")
        }
    }
}

class Main {
    let animalSoundService: AnimalSoundService
    
    init(animalSoundService: AnimalSoundService) {
        self.animalSoundService = animalSoundService
    }
    
    func startMain() {
        animalSoundService.hello()
    }
}
```



<u>*After*</u>

* 기능이 추가 되었을 때에도 코드 수정없이 확장이 가능하게 된다

```swift
protocol Animal {
    func speak()
}

final class Cat: Animal {
    func speak() {
        print("야옹")
    }
}
final class Dog: Animal {
    func speak() {
        print("멍멍")
    }
}
final class Tigger: Animal {
    func speak() {
        print("어흥")
    }
}

final class AnimalSoundService {
    var animalType: Animal
    
    init(animalType: Animal) {
        self.animalType = animalType
    }
    
    func hello() {
        animalType.speak()
    }
}

final class Main {
    let animalSoundService: AnimalSoundService
    
    init(animalSoundService: AnimalSoundService) {
        self.animalSoundService = animalSoundService
    }
    
    func startMain() {
        animalSoundService.hello()
    }
}

let cat = Cat()
let animalSoundService = AnimalSoundService(animalType: cat)
let main = Main(animalSoundService: animalSoundService)
main.startMain()
```



<br/>

#### *Liskov Substitution Principle*

* 부모 클래스로 동작 하는 곳에서 자식 클래스 인스턴스를 넣어줘도 대체 가능 하도록 설계 해야 한다는 법칙
* 해결방안으로 protocol + extesion 으로 구현된 기능을 넘기는 것도 가능 
* 개인적으로는 위반을 안하고 프로그래밍 하는건 어렵다고 생각. 근거 있는 판단이 중요

```swift
class Animal {
  func eat() {
    print("먹이 찾기")
  }
  func sleep() {
    print("잠 10시에 자기")
  }
}

class Rion: Animal {
	func poop() {
    print("응가 하기")
  }
}

let animal = Animal()
let rion = Rion()

animal.eat() == rion.eat()
animal.sleep() == rion.sleep()
```

<br/>

#### *Interface Segregation Principle*

* 불필요한 인터페이스는 구현 하지 말아야 한다. --> 즉 필요 단위로 나누자

<u>*Before*</u>

```swift
protocol SoccerPlayer {
    func shoot()
    func tackle()
    func block()
}
```

<u>*After*</u>

```swift
protocol shootAble {
    func shoot()
}
protocol tackleAble {
    func tackle()
}
protocol blockAble {
    func block()
}
```

```swift
class Stricker: shootAble, tackleAble {
    func shoot() {
        // code..
    }
    func tackle() {
        // code..
    }
}
class GoalKeeper: blockAble, tackleAble  {
    func tackle() {
        // code..
    }
    func block() {
        // code..
    }
}
```

<br/>

#### *Dependency Inversion Principle*

> Dependency Inversion 의존성주입
>
> * 객체간의 결합도를 낮추면서 유연한 코드를 작성 할 수 있다
> * 이로써, Unit Test 가 용이 하고, 코드의 재활용성을 높여준다

* 상위 모듈의 하위 모듈에 의존하면 안되고, 추상체에 의존하게 해야 한다.
* 즉 구현체 끼리 의존하지 말고 추상화된 인터페이스에 의존 하자는것

![스크린샷 2023-01-13 00.14.56](/Users/haha1haka/Desktop/Workspace/정리정리정리/0.imageServer/스크린샷 2023-01-13 00.14.56.png)

* Without DIP Diagram 에서 상위 모듈(구현체)은 하위 모듈에 의존 하는 식으로 진행
* 이때 Swift 파일이 3500개면 어질어질
* With DIP Diagram 에서 는 상위와 하위 모듈이 추상체에 의존하게 함으로써 화살표 방향 역전(Inversion)
* 이로써 단위테스트가 유연하고, 강한 결합을 끊음으로써 재사용시 유지보수 용이 등 

<u>*Before*</u>

* `Solider`는 `Gun`에 의존 -->  DI를 통한 객체간의 결합을 끊어 줘야함
* 또한 DIP를 이용하여 추상화된 타입에 의존하게 함으로써 객체 재활용

```swift
class Gun {
    private var damage: Int
    
    init(damage: Int) {
        self.damage = damage
    }
    func attack() -> Int {
        return damage
    }
}

class Knife {
    // code ..
}
class fist {
    // code ..
}

class Soldier {
    private var name: String
    private var hp: Int
    private var weapon = Gun(damage: 12)
    
    init(name: String, hp: Int) {
        self.name = name
        self.hp = hp
    }
    
    func attack() -> Int {
        return weapon.attack()
    }
}

let soldier = Soldier(name: "jack", hp: 999)
```

<u>*After*</u>

```swift
protocol WeaponAvailable {
    func attack() -> Int
}

class Gun: WeaponAvailable {

    private var damage: Int
    
    init(damage: Int) {
        self.damage = damage
    }
    
    func attack() -> Int {
        return damage
    }

}

class Knife {
    // code..
}
class fist {
    // code ..
}

class Soldier {
    private var name: String
    private var hp: Int
    private var weapon: WeaponAvailable
    
    init(name: String, hp: Int, weapon: WeaponAvailable) {
        self.name = name
        self.hp = hp
        self.weapon = weapon
    }
    func attack() -> Int {
        return weapon.attack()
    }
}


let k2 = Gun(damage: 12)
let soldier1 = Soldier(name: "jack", hp: 999, weapon: k2)
```

<br/>

> It will still be lacking. Code reviews are welcome. --> [haha1haka@icoud.com](haha1haka@icoud.com)





