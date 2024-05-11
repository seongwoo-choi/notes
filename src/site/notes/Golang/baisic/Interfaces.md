---
{"dg-publish":true,"permalink":"/Golang/baisic/Interfaces/","created":"2024-01-27T22:59:10.000+09:00","updated":"2024-05-11T11:34:10.396+09:00"}
---

고언어에서 인터페이스의 역할은 객체의 동작을 표현하는 것이다.
인터페이스가 표현한 대로 동작하는 객체는 인터페이스로 사용할 수 있다.
인터페이스는 각 타입이 실제로 내부에 어떻게 구현되어 있는지 말하지 않고, 단순한 동작 방식만 표현한다.

인터페이스의 이런 특징은 추상 메커니즘을 제공한다. 함수나 메서드의 매개변수로 인터페이스를 사용하는 것은 "값의 타입이 무엇인지"보다 오직 "값이 무엇을 할 수 있는지"에만 집중하게 해준다.

고언어의 인터페이스는 덕 타이핑 방식을 채택했다. 
덕 타이핑 방식은 객체의 변수나 메서드의 집합이 객체의 타입을 결정한다.
즉, 인터페이스에 걸맞은 메소드를 가진 타입은 인터페이스로 사용할 수 있다.

인터페이스의 이러한 특징 덕분에 정적 타입 언어인 고언어를 유연하게 사용할 수 있다.

## 인터페이스 정의

```golang
type 인터페이스명 interface {
	메서드1(매개변수) 반환타입
	메서드2(매개변수) 반환타입
}
```

인터페이스에 정의된 메서드와 서명이 같은 메서드를 가진 타입은 인터페이스로 사용할 수 있다.

area() 메서드를 가진 shaper 인터페이스를 정의한다. 그리고, shaper 인터페이스를 매개변수로 받아, shaper 에 있는 area() 메서드의 실행 결과를 출력하는 describe() 함수를 정의한다.

```golang
type shaper interface {
	area() float64
}

func describe(s shaper) {
	fmt.Println("area :", s.area())
}
```

rect 구조체와 area() 메서드를 정의한다.

```golang
type rect struct{ width, height float64 }

func (r rect) area() float64 {
	return r.width * r.height
}

func main() {
	r := rect{3, 4}
	describe(r) // area : 12
}
```

rect 구조체와 shaper 인터페이스는 코드 상 아무런 연결 고리가 없다. 
rect 타입이 shaper 인터페이스에 정의된 메서드 area() 를 구현하기 때문에, rect 타입 값을 shaper 인터페이스로 사용할 수 있게 된 것이다.

즉, 특정 인터페이스의 메서드를 특정 타입이 구현하게 되면, 해당 타입은 인터페이스로 사용할 수 있게 된다.

## 인터페이스 네이밍 컨벤션

인터페이스 이름은 메서드 이름에 er(또는 r)을 붙여서 짓는다. 예를 들면, Printer, Reader, Writer, Logger, Converter 등이 있다.

인터페이스는 짧은 단위로 만든다. Go 의 기본 라이브러리에서도 메서드를 하나만 정의한 인터페이스가 대부분이다.
인터페이스에 정의된 메서드는 많아도 세 개를 넘지 않게 한다.

Go 기본 라이브러리에 정의된 인터페이스를 살펴보자.

io 패키지의 Reader 인터페이스
```golang
type Reader interface {
	Read(p []byte) (n int, err error)
}
```

io 패키지의 Writer 인터페이스
```golang
type Writer interface {
	Write(p []byte) (n int, err error)
}
```

io 패키지의 Closer 인터페이스
```golang
type Closer interface {
	Close() error
}
```

## 익명 인터페이스

인터페이스도 타입으로 정의하지 않고 익명으로 사용할 수 있다.
```golang
func display(s interface{ show() }) {
	s.show()
}
```

display() 함수의 매개변수 타입을 interface { show() } 로 정의했다. 
다시 말해서, display() 함수에는 show() 메서드를 갖는 타입을 매개변수로 전달할 수 있다.

```golang
type rect struct { width, height float64 }

func (r rect) show() {
	fmt.Printf("width: %f, height: %f\n", r.width, r.height)
}

type circle struct { redius float64 }

func (c circle) show() {
	fmt.Printf("radius: %f\n", c.radius)
}

func main() {
	r := rect{3, 4}
	c := circle{2.5}
	display(r) // width: 3.000000, height: 4.000000
	display(c) // radius: 2.500000
}
```

## 빈 인터페이스

interface{} 타입은 메서드를 정의하지 않은 인터페이스이다. interface{} 에는 정의된 메서드가 없어서 어떤 값이라도 interface{} 로 사용할 수 있다.

즉, 함수나 메서드의 매개변수를 interface{} 타입으로 정의하면 어떤 값이든 전달받을 수 있다.

```golang
func main() {
	r := rect{3, 4}
	c := circle{2.5}
	display(r) // width: 3.000000, height: 4.000000
	display(c) // radius: 2.500000
	display(2.5) // 2.5
	display("rect struct") // rect struct
}

func display(s interface{}) {
	fmt.Println(s)
}
```

## 다형성

다형성이란 다양한 타입의 객체가 같은 메시지를 통해 다양한 방식으로 동자갛게 하는 것을 말한다.
메서드 오버라이딩을 통해 서로 다른 객체가 똑같은 메소드를 사용해 다양한 메세지를 전달할 수 있게 하는 것.

고언어에는 서브 타이핑과 오버로딩이 없다. 고언어는 인터페이스로 다형성을 지원한다.

고언어의 인터페이스는 단순한 메서드 서명의 묶음이고, 인터페이스에 정의된 메서드와 서명이 같은 메서드를 가진 타입은 인터페이스로 사용할 수 있다. 메서드와 인터페이스 사이에 명확한 연결 고리는 없다.

타입에 정의된 메서드, 즉 객체가 동작하는 방식에 따라 인터페이스와 is-A 관계를 형성한다.

다시 말해 인터페이스를 사용하면 타입이나 메서드의 구현 방식과 관계없이 다양한 값을 같은 형태로 다룰 수 있다.

## 인터페이스를 통한 다형성

아래 그림처럼 타입이 다른 구조체 세 개를 정의하고, 인터페이스로 아래 구조체 세 개를 같은 방식으로 처리해보자.
![interfaces1.png](/img/user/Golang/static/interfaces1.png)

먼저 Const() float64 메서드 서명을 가진 Conter 인터페이스를 만들고, Conster 인터페이스를 매개변수로 받아 Conster 인터페이스의 Const() 를 출력하는 displayConst() 함수를 만든다.

```golang
type Coster interface {
	Cost() float64
}

func displayCost(c Conster) {
	fmt.Println("const: ", c.Const())
}
```

Item 타입과 DiscountItem 타입을 정의하고, 각 타입에 Const() float64 메서드를 정의한다.

```golang
type Item struct {
	name string
	price float64
	quantity int
}

func (i Item) Cost() float64 {
	return i.price * float64(i.quantity)
}

type DiscountItem struct {
	Item
	discountRate float64
}

func (d DiscountItem) Cost() float64 {
	return d.Item.Const()
}
```

이제 Rental 타입을 새로 추가하고 Rental 타입에도 Const() 메서드를 정의해보자.

```golang
type Rental struct {
	name string
	feePerDay float64
	periodLength int
	RentalPeriod
}

type RentalPeriod int

const (
	Days RetalPreiod = iota
	Weeks
	Months
)

func (p RentalPeriod) ToDays() int {
	switch p{
	case Weeks:
		return 7
	case Months:
		return 30
	default:
		return 1
	}
}

func (r Rental) Cost() float64 {
	return r.feePerDay * flaot64(r.periodLength * r.ToDays())
}
```

main 함수에서 Item 과 Rental 타입 값을 생성하고, 마찬가지로 생성한 값을 displayCost() 함수에 전달하여 Cost() 를 출력한다.

```golang
type Coster interface { 
	Cost() float64 	
} 

func displayCost(c Conster) { 
	fmt.Println("const: ", c.Const()) 
}

func main() {
	shirt := Item{"슈프림", 250000, 3}
	vedio := Rental{"인터스텔라", 1000, 3, Weeks}

	displayCost(shirt)
	displayCost(vedio)
}
```

코드에서 Item 과 Rental 타입은 Coster 인터페이스와 아무런 연결 고리가 없다. Item 과 Rental 타입에 Coster 인터페이스에 정의된 메서드와 형태가 같은 Cost() 메서드가 정의되어 있을 뿐이다.

다시 말해 Item 과 Rental 타입은 Cost() 메서드를 통해 인터페이스에서 정의한 것과 같은 방식으로 사용될 수 있고, 다시 말해 Item 과 Rental 타입이 Coster 인터페이스로 사용될 수 있다는 것을 의미한다.