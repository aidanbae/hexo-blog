---
title: golang-design-p-01
catalog: true
date: 2018-03-18 22:16:10
subtitle:
header-img:
tags:
---

# Golang Design Pattern - Bridge Pattern
>고랭 디자인패턴을 공부하고있습니다.
이왕하는김에 블로그에 정리를 해볼까합니다.
작심삼일을 여러번해보겠습니다.

---
## 브릿지 패턴
Bridge Pattern은 두 구현체 간의 강한 결합을 제거하기 위해서 사용하는 패턴입니다.
두 클래스 모두 추상화된 상위 클래스 또는 인터페이스를 가지게 되고, 의존성은 상위 클래스만 가지게됩니다.

잘 이해가 안가시죠?
저도 이해가안되서 준비했습니다.
제가 공부하고 있는 Golang Design패턴책은 프린트 예제가 있었는데
이해가 더 어렵다고 생각해 좋은 예제를 찾다가
게임개발에 맞는 예제 블로그를 찾았습니다.(아쉽게도 자바)
http://effectiveprogramming.tistory.com/entry/Bridge-%ED%8C%A8%ED%84%B4
![hw](image1.png)
_여기있는 무기 핸들러와 무기 간의 브릿지 패턴을 예로 Golang에 적용해서 이해해보았습니다._

위 다이어그램은 Weapon과 WeaponHandler라는 상위 인터페이스를 보여줍니다.
의존관계는 이 인터페이스들 간에만 존재합니다.
Weapon을 구현한 Bow와 Sword는 실제 자신들을 다룰 구체적인 녀석을 모릅니다.
WeaponHandler를 구현한 Warrior나 Smith도 자신들이 다룰 구체적인 녀석을 모릅니다.
참 재밌죠?

즉, 무기 관련 개발자는 Weapon의 인터페이스에 맞게 개발하면되고
무기핸들러 관련 개발자는 WeaponHandler의 인터페이스에 맞게 개발하면 됩니다.
각각의 구체적인 클래스들에는 변경에 있어서 영향을 주지않습니다.

다음은 위 사항을 구현한 Golang 코드입니다.
```golang
type Weapon interface {
	Attack()
	Repair()
}

type Bow struct {}
func(b *Bow) Attack() {
	println("bow Attack")
}
func(b *Bow) Repair() {
	println("bow Repair")
}

type Sword struct {}
func(s *Sword) Attack() {
	println("sword Attack")
}
func(s *Sword) Repair() {
	println("sword Repair")
}
```
Weapon의 인터페이스에 호응하기위해서는 구조체에 Attack과 Repair라는 리시버함수만 달려있으면 됩니다.
```golang
type WeaponHandler interface {
	Handle()
}

type Warrior struct {
	weapon Weapon
}
func(w *Warrior) Handle() {
	w.weapon.Attack()
}

type Smith struct {
	weapon Weapon
}
func(s *Smith) Handle() {
	s.weapon.Repair()
}
// WeaponHandler 인터페이스를 인자로 받는 함수를 만들어봅시다!
func doit(w WeaponHandler) {
	w.Handle()
}
```
WeaponHandler의 인터페이스는 Handle이라는 메서드만 들고있으면 됩니다.
각각의 구체적인 구현체는 자신의 역할에 맞는 일을 수행합니다.
Warrior는 무기로 공격하므로 Attack을 수행
Smith는 무기를 수리하므로 Repair를 수행합니다.

각각의 구현체는 자신이 들고있는 무기가 뭔지몰라도 Handle이라는 해당 메소드를 호출합니다.

```golang

package structural

import "testing"

func TestSmith_Handle(t *testing.T) {
	sword := &Sword{} // 칼을만들고
	smith := &Smith{sword} // 스미스에게 칼을 줍니다
	smith.Handle() // 결과: "sword Repair"

	warrior := &Warrior{sword} // 워리어에게 칼을 줍니다
	warrior.Handle() //  결과: "sword Attack"

	doit(warrior) // "sword Attack"
	doit(smith)  // "sword Repair"
}
```
테스팅 코드를 돌려봅니다. 훌륭하죠!?

이러한 방식으로 무기의 종류를 늘리거나 무기를 다루는 사람을 늘려도 다른 한쪽에
영향을 주지않도록 만들수 있습니다

강력한 디커플링 패턴인 브릿지패턴~ 여러분도 사용해보세요!
다음시간에는 어댑터패턴을!
