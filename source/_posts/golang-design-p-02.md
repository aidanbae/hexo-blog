---
title: golang-design-p-02
catalog: true
date: 2018-03-23 20:20:44
subtitle:
header-img:
tags:
---
# Golang Design Pattern - Command Pattern
>이번엔 명령패턴입니다!!
그림판이나 Photoshop은 모든 액션들이 우리들(유저)들의 마우스Input과 연관이 큽니다.
액션 하나 하나가 다 명령이죠.
Ctrl+z키라는 마법의 주문으로 우리는 우리가했던 액션들을 하나하나씩 취소할 수 있습니다.
이를 가능하게 한것이 명령패턴입니다.
요구사항에 대한 캡슐화와 요구사항을 큐에 저장하거나 기록, 취소 할수 있다는 장점이있죠!
---

## 커맨트패턴

제가 게임프로그래밍을 접하면서 첫 디자인패턴을 공부했을때 만난 패턴입니다.
글쓴이가 엄청 효율적이라면서 썰을 풀었지만 이해가 안됐었어요
이제는 조금 이해가갑니다.
너무 어렵게 생각하지마세요! Simple is best

> 유저의 요청사항(명령) 캡슐화!
> 요청사항과 큐를 이용해서 작업내역을 관리!

그냥 가볍게 위 두가지만 생각하면 편하더라구요
시작해볼까요

우선 `Command` Interface를 구현해봅시다.
스터디한 책에는 ConsoleCommand와 Excute만 있지만 그대로하면 재미없죠
실수가 많은 저는 `Ctrl+z`의 광팬이니깐
Undo도 추가해보겠습니다! ConsolePrint를 취소하는 것은 와닿지 않으니
간단하게 `계산기명령`으로 해보면 어떨까요?
답정너죠. 플러스와 마이너스 Command를 구현해보겠습니다.

```golang

type Command interface {
	Execute(*Calculator)
	Undo(*Calculator)
}

type PlusCommand struct {
	beforeVal int
	num       int
}

func (a *PlusCommand) Execute(calculator *Calculator) {
	a.beforeVal = calculator.val
	calculator.Add(a.num)
	fmt.Println(calculator.val)
}

func (a *PlusCommand) Undo(calculator *Calculator) {
	calculator.val = a.beforeVal
	fmt.Println(calculator.val)
}
```

Command interface는 `Execute`와 `Undo` 두가지 메소드로 이루어져있습니다.
두개의 함수가 리시버함수로 달려있기만하면 인터페이스에 부합하는 것이죠
인자로 `명령을 실행하는 주체가 될 Actor(계산기)`를 받습니다.

>PlusCommand(더하기 요청사항)가 하는 짓은 간단합니다.
Execute: 계산기에 있는 이전 값을 저장해두고(기록) 계산기의 Value에 특정값을 더하는 것
Undo: 이전 값으로 계산기의 Value를 변경

마이너스는 더 쉽겠죠 똑같습니다
```golang
type MinusCommand struct {
	beforeVal int
	num       int
}

func (m *MinusCommand) Execute(calculator *Calculator) {
	m.beforeVal = calculator.val
	calculator.Minus(m.num)
	fmt.Println(calculator.val)
}

func (m *MinusCommand) Undo(calculator *Calculator) {
	calculator.val = m.beforeVal
	fmt.Println(calculator.val)
}
```

읽다보니 계산기는 왜 설명안해주세요 하는 분들이 있을까바~
제가 그냥 임의로 add와 minus로 구조화한겁니다 Simple하죠

```golang
type Calculator struct {
	val int
}
func (c *Calculator) Add(num int) {
	c.val += num
}
func (c *Calculator) Minus(num int) {
	c.val -= num
}
```

이제 명령패턴을 정복할 거의 모든 준비가 끝났습니다.
그나마 필요한게 요청사항(작업내역)을 관리할 큐정도겠네요.

작업내역을 저장할 큐슬라이스와 Actor를 품고 있는 `CommandQueue구조체`를 만들어봅시다 :
```golang
type CommandQueue struct {
	queue []Command
	actor *Calculator
}

func (p *CommandQueue) AddCommand(c Command) {
	// 명령을 큐에 저장하고
	p.queue = append(p.queue, c)
	// 실행합니다
	c.Execute(p.actor)
	// 길이가 10이 될경우 작업내역을 지웁니다
	if len(p.queue) == 10 {
		p.queue = make([]Command, 10)
	}
}

func (p *CommandQueue) RemoveCommand() {
	// 마지막 명령을 꺼내서 Undo를 호출합니다.
	lastCommand := p.queue[len(p.queue)- 1]
	lastCommand.Undo(p.actor)
	// 마지막 명령을 큐슬라이스에서 제거합니다.
	p.queue = p.queue[:len(p.queue)-1]
}

func main() {
	calculator := &Calculator{val:0} // 0으로 시작하는 계산기
	queue := CommandQueue{actor: calculator} // 계산기를 액터로 지정후 커맨드큐생성

	queue.AddCommand(CreatePlusCommand(3)) // +3
	queue.AddCommand(CreatePlusCommand(3)) // +3
	queue.AddCommand(CreatePlusCommand(3)) // +3
	queue.AddCommand(CreateMinusCommand(3)) // -3
	queue.AddCommand(CreateMinusCommand(3)) // -3
	queue.RemoveCommand() // 돌려돌려 되돌려줘
	queue.RemoveCommand() // 돌려돌려 되돌려줘...
}

```
AddCommand는 명령을 축적!
RemoveCommand 명령을 제거!

결과는!!!?
!['hoho'](screenshot.png)
작업취소까지 너무 좋네요
저도 구현하면서 재밌었습니다

이번에 개발하는 서버에 적용해볼게요!
go-redis에서 Cmdable이 이런식으로 동작하는거 같더라구요
꼭 실무, 프로젝트에 적용해서 내것으로 만들어보세요.
다음시간에는 더 재미있는 새로운 패턴으로 찾아뵙겠습니다.
