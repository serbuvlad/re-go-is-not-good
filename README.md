RE: go-is-not-good
==================

A response to the [go-is-not-good](https://github.com/ksimka/go-is-not-good) repo from a go fan. Some or my responses are breif, perhaps overly so.

The points
----------

###Good points

> unexpected variable shadowing

Yeah, this sucks ass.

> no language interoperability (only C)

Fair point.

###Dependency managment/GOPATH/Packages

> bad dependency management

> GOPATH is a mess

> import-based vendoring is terrible

> package mechanism is broken

> no versioning model

GOPATH and vendoring worked well enough for most people, but we use modules now which is much better so all these points are obsolete. GOPATH is basically just a cache now and you need not care about it.

> different build approaches

No./Not anymore.

> no unused imports

Use the goimports tool before compiling. It'll clean it up.

> no subpackages

Reminder that C++'s `protected` is an admitted mistake.

> can't import packages relatively

You can but you shouldn't.


###Language design

> c-style

Good if you like C.

> stuck in Unix thinking

Good if you like Unix.

> stuck in 70's

Translation: avoids most of the traps of modern software desing.

> poor design

🤡

> no OOP

Type hierarchies aren't the only way to do OOP. In Go you're working with objects and you compose them far more than in most so-called OOP languages.

> polymorphism is broken

No. It functions quite well.

> upper-case/lower-case scoping is bad

It's expressive. Rip Japanese codebases though.

> designed for stupid people

Designed for fast development. Implementing a hard algorithm is hard in any language. Implementing an easy algorithm, or combining existing ones, should be easy in high level languages.

> no semicolons at line endings

Ha!

> is compiled

Ha!

> concurrency and parallelism are mixed

Mixed as in the same API? This reflects underlying hardware (no GPGPU support :( ). You have specialized constructs in `sync`.

> garbage collector is nothing new; concurrent mark/sweep from the '70s

> goroutine is not original and revolutionary

A reliable way to have good ideas is to be unoriginal. The GC is pretty good though https://blog.twitch.tv/en/2016/07/05/gos-march-to-low-latency-gc-a6fa96f06eb7/

> garbage collector requires implicit (hidden) tradeoffs

Yes.

###Language features

> no virtual functions

No inheritance so what'd be the point?

> inconvenient `range`

`range` is an afterthought and should be treated as syntactic sugar. Don't be afraid to use the C-style `for` for more control.

> `new` and `make` instead of one

new creates an object of any type of zero value and returns a pointer to it. make creates slices maps and channels. The type of `new(map[K]T)` is `*map[K]T` and the map itself is nil. The type of `make(map[K]T)` is `map[K]T` and the map actually exists. Different functions that do different things. `new` is also quite rare to see in code as 99% of the time there's a cleaner syntax to obtain that pointer. `s := new(StructT)` is better written as `s := &StructT{}` for consistency with `s := &StructT{ /* fields ; ones not mentioned are zero*/ }`. 

> hard to extend

It's not meant to be.

> bad unicode support

No. Made by the UTF-8 creators too.

> sort.Interface approach is clumsy

Debatable. I don't mind it.

> defer is abused

Defer is good but you need to learn how to use it so as not to leak. It's not used as often as you think in the wild. (Also we have zero cost defers for most cases now, yay!)


> channels are slow

Compared to what and in what use case? You can have hundreds of thousands of goroutines orchestrated with channels on your x86 laptop and still run pretty well. Maybe there are better solutions for certain neiches, like worker pools or whatever, but nothing this generic which scales this well.

> channel API is inconsistent

No.

> can't declare/validate implements interface (hidden types)

Declare, no, and this is a feature. Validate yes.

```Go
func TestTisI(t *testing.T) {
    var x T
    _ = I(x) // shouldn't even compile if T does not implement I
}
```

But you shouldn't do this.

> no sum types

Use interfaces.

> no algebraic data types

Not built-in, no.

> no asserts

Use Testing and runntime checks.

> no macros or templates

Good.

> no immutables

Just don't mutate it.

> no constructors

No implicit constructors. Most of the time the zero value will do. When it doesn't `s := NewT()`.

> no pattern matching
> no function/operator overloading

Good. Also means you don't need a symbol table. 100k lines a second compilation go brrrrrr.

> no ternary operator

Translation: no hidden control flow/only one way to do things. Good.

> no generics

You don't really need them. And they'll be coming soon (TM).

###Pointers

> has pointers

> nil as a failure marker

Pointers in Go are basically nil-able references so no arithmetic. Their inclusion has positive (structures containing pointers can be created without a constructor, easy optionals etc.) and negative trade-offs. In my opinion the positives far outweight the negatives.

> no non-nullable types

Structs, ints, floats 🙂.

> pointers are a mess

No.

> nil interfaces are not entirely nil

Here's his code.

```Go
package main

import "fmt"

type Err struct{}

func (Err) Error() string {
	return "error message"
}

func Generate() *Err {
	return nil
}

func Test() error {
	return Generate()
}

func main() {
	if Test() != nil {
		fmt.Println("Wtf?")
	}
}
```

It prints wtf, like it should. Interfaces are not nil if the wrapped type is nil. That would be ass.

###STL

> lack of basic data structures

> no map/reduce/filter

Muh "I can't program without STL". I encourage you to try.

> unwieldy to code new collections

Write fewer of them.

> can't change hash function in maps

Patch the compiler or make your own map. The standard library does this for `sync.Map` so it's not like a "one rule for me another for thee" situation.

> no user-type iteration

See `range` (use the clasic `for`)


###Errors

> error handling

I think it's good.

> panic instead of exceptions

> no exceptions

Panic is not used for error handling. It should only happen on programmer error or in 2 + 2 = 5 state situations. Otherwise you use `error`s. "Don't panic" is a [go proverb](https://go-proverbs.github.io/). Explicit errors are much better as anyone who has ever seen a `java.lang.NullPointerException` or a stack trace blow up in their face can atest to.


###Misc.

> summary: not elegant as Python, not strong as Java

Python aka "who knows what the data types are" the language?

“Porting my code review tools to Go from Python. Surprised to see a reduction in line counts.” – Scott Dunlop

> compilation rules are too confining

No warnings so some things (like unused variables) are errors instead. Jaring at first but a good simplification and you get used to it.

> confusing and undebuggable

No.

> confusing/stupid syntax

Confusing? No. Stupid? Your call.

> cumbersome interface

As opposed to what language?

> difficult to generate code automatically

Maybe. Haven't tried it. There is tooling support though (go:generate and the ast in the standard library).

> garbage collector optimized for pause times at the cost of other desirable gc features

Perhaps. Don't know enough about said trade-offs to comment.

> hard to test, hard to mock

Hard to test, no. Hard to mock, maybe. Just write the code and let the structure flow. 

> hostile to developer ergonomics

Your call.

> too young

In 2012.

> the worst compiler toolchain ever (2014)
> immature GC (2014)
> immature toolchain (2014)
> not stable
> questionable compiler rigidity (2015)

Toolchain was rewritten in 2015. The one before was odd and based on the Plan 9 compilation system. So quite different to what most people are used to. The new toolchain is much more intuitive for users. It was probably unstable at first, but now you have Go code running in production (see Docker, [go.dev](https://go.dev) examples).

> misleading marketing around garbage collector

\...

> multiple return parameters are overrated

Better than `OUT` paramaters, `errno` or wrapping return types.

> multiple return values have no type checking

False.

> no `this`

No implicit `this`.

> no decent IDE

GoLand, VSC, editor+shell

> no first-class support of interfaces

What? Yes there is.

> no first-class tuple

Expressions have multiple values and structures are named tuples so who cares?

> no sugar for slices

There is some.

> not-so-obvious slices behaviour

Depends what you're used to. You learn it.

> poor std math lib

Maybe. I don't have extraordinary math needs, but maybe it's a fair point, who knows?

> project layout is bad

Your call.

> psuedointellectual arrogance of Rob Pike and everything he stands for

K.

> slow json parsing

It uses reflect so this is probably true, but it wouldn't surrprise me if it isn't either. I should check. On the other hand the interface is really really really cool.

Fixed.

> too opinionated

"Do you want everyone to vote for the same political party, too?" - theo

> too simple / lack of syntactic sugar

Good.

> too verbose

Not in practice.

> type inference is too simple

Good.

> un-googlable name

"Golang".

> weird mascot (gopher)

![Dancing Gopher](https://camo.githubusercontent.com/01ee857f12b45ddd41369434b2954ba463c5b29e/687474703a2f2f7374617469632e76656c76657463616368652e6f72672f70616765732f323031382f30362f31332f70617274792d676f706865722f70617274792d676f706865722e676966)
