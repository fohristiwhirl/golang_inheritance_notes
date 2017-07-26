# Golang inheritance notes

You can fake a sort of inheritance in Golang as follows.

Make each "class" (really a struct typedef) have a corresponding interface. The "class" implements all the methods of the interface.

You can create a "subclass" by embedding the class in a new type, which should also have its own interface (with additional methods if desired). The new "subclass" must now implement all those additional methods, but it can also override methods of its parent class.

You can check whether a "class" is a subclass of something by type-asserting on the interfaces (NOT on the concrete types).

```golang

package main

import "fmt"

// ------------------------

type Animaler interface {
  Speak()
}

type Animal struct {
  Name string
}

func (a *Animal) Speak()  {
  fmt.Printf("%s says: <ill-defined mumblings>\n", a.Name)
}

// ------------------------

type Birder interface {
  Animaler
  Fly()
}

type Bird struct {
  Animal
}

func (b *Bird) Fly() {
  fmt.Printf("%s is flying!\n", b.Name)
}

// ------------------------

type Parrot struct {
  Bird
}

func (p *Parrot) Speak() {
  fmt.Printf("%s says: Squawk!\n", p.Name)
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

// ------------------------

func NewSquirrel() *Animal {
  ret := Animal{
    Name: "Jimmy",
  }
  return &ret
}

// ------------------------

func main() {

  var parrot Animaler = NewParrot()
  
  parrot.Speak()
  
  tmp, ok := parrot.(Birder); if ok {
    tmp.Fly()
  } else {
    fmt.Printf("This thing can't fly\n")
  }
  
  var squirrel Animaler = NewSquirrel()
  
  squirrel.Speak()
  
  tmp, ok = squirrel.(Birder); if ok {
    tmp.Fly()
  } else {
    fmt.Printf("This thing can't fly\n")
  }
}

```
