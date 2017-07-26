# golang_inheritance_notes

You can fake a sort of inheritance in Golang as follows.

Make each "class" (really a struct typedef) have a corresponding interface. The "class" implements all the methods of the interface.

You can create a "subclass" by embedding the class in a new type, which also has its own interface with additional methods if desired. The new "subclass" must now implement all the additional methods of the new interface, but it can also override methods of its parent class.

You can check whether a "class" is a subclass of something by type-asserting the interfaces.

```golang

package main

import "fmt"

type Animaler interface {
  Speak()
}

type Animal struct {
  Name string
}

func (a *Animal) Speak()  {
  fmt.Println("ill-defined mumblings")
}

type Bird struct {
  Animal
  Flying bool
}

func (b *Bird) Fly() {
  b.Flying = true
}

type Birder interface {
  Animaler
  Fly()
}

type Parrot struct {
  Bird
}

func (p *Parrot) Speak() {
  fmt.Println("Squawk!")
}

func NewParrot() *Parrot {
  ret := Parrot{
    Bird: Bird{
      Animal: Animal{
        Name: "Polly",
      },
    },
  }
  return &ret
}

func NewSquirrel() *Animal {
  ret := Animal{
    Name: "Jimmy",
  }
  return &ret
}

func main() {

  var parrot Animaler = NewParrot()
  
  parrot.Speak()
  p, ok := parrot.(Birder); if ok {
    p.Fly()
  }
  
  var squirrel Animaler = NewSquirrel()
  
  squirrel.Speak()
}

```
