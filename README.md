RE: go-is-not-good
==================

A response to the [go-is-not-good](https://github.com/ksimka/go-is-not-good) repo from a go fan. Some or my responses are breif, perhaps overly so.

The points
----------

> GOPATH is a mess (2014)
Worked well enough for most people, but we use modules now which is much better. GOPATH is basically just a cache now and you need not care about it.

> `new` and `make` instead of one
new creates an object of any type of zero value and returns a pointer to it. make creates slices maps and channels. The type of `new(map[K]T)` is `*map[K]T` and the map itself is nil. The type of `make(map[K]T)` is `map[K]T` and the map actually exists. Different functions that do different things. `new` is also quite rare to see in code as 99% of the time there's a cleaner syntax to obtain that pointer. `s := new(StructT)` is better written as `s := &StructT{}` for consistency with `s := &StructT{ /* fields ; ones not mentioned are zero*/ }`. 

> panic instead of exceptions
Panic is not used for error handling. It should only happen on programmer error or in 2 + 2 = 5 state situations. Otherwise you use `error`s. "Don't panic" is a go proverb https://go-proverbs.github.io/

> bad dependency management
Fixed. See GOPATH.

> bad unicode support
No. Made by the UTF-8 creators too.

> c-style
Good if you like C.

> can't change hash function in maps
Patch the compiler or make your own map. The standard library does this for `sync.Map` so it's not like a "one rule for me another for thee" situation.

> can't declare/validate implements interface
> hidden types
Declare, no, and this is a feature. Validate yes.

```Go
func TestTisI(t *testing.T) {
    var x T
    _ = I(x) // shouldn't even compile if T does not implement I
}
```

But you shouldn't do this.

> can't import packages relatively
You can but you shouldn't.

> channel API is inconsistent
No.

> channels are slow
Compared to what and in what use case? You can have hundreds of thousands of goroutines orchestrated with channels on your x86 laptop and still run pretty well. Maybe there are better solutions for certain neiches, like worker pools or whatever, but nothing this generic which scales this well.

> compilation rules are too confining
No warnings so some things (like unused variables) are errors instead. Jaring at first but a good simplification and you get used to it.

> concurrency and parallelism are mixed
Mixed as in the same API? This reflects underlying hardware (no GPGPU support :( ). You have specialized constructs in `sync`.

> confusing and undebuggable
No.

> confusing/stupid syntax
Confusing? No. Stupid? Your call.

> cumbersome interface
As opposed to what language?

> defer is abused
Defer is good but you need to learn how to use it so as not to leak. It's not used as often as you think in the wild. (Also we have zero cost defers for most cases now, yay!)

> designed for stupid people
Designed for fast development. Implementing a hard algorithm is hard in any language. Implementing an easy algorithm, or combining existing ones, should be easy in high level languages.

> different build approaches
No./Not anymore.

> difficult to generate code automatically
Maybe. Haven't tried it. There is tooling support though (go:generate and the ast in the standard library).

> error handling
I think it's good.

> garbage collector is nothing new; concurrent mark/sweep from the '70s
> goroutine is not original and revolutionary
A reliable way to have good ideas is to be unoriginal. The GC is pretty good though https://blog.twitch.tv/en/2016/07/05/gos-march-to-low-latency-gc-a6fa96f06eb7/

> garbage collector optimized for pause times at the cost of other desirable gc features
Perhaps. Don't know enough about said trade-offs to comment

> garbage collector requires implicit (hidden) tradeoffs
Yes.

> hard to extend
It's not meant to be.

> hard to test, hard to mock
Hard to test, no. Hard to mock, maybe. Just write the code and let the structure flow. 

> has pointers
Pointers in Go are basically nil-able references so no arithmetic. Their inclusion has positive (structures containing pointers can be created without a constructor, easy optionals etc.) and negative trade-offs.

> hostile to developer ergonomics
Your call.

> immature GC (2014)
> immature toolchain (2014)
Not anymore (see the twitch post). The language was 5 years old at the time, give it a break.

> import-based vendoring is terrible
Fixed. See GOPATH.

> inconvenient `range`
`range` is an afterthought. Don't be afraid to use the C-style `for` for more control.

> is compiled
Ha!

> lack of basic data structures
Muh "I can't program without STL". I encourage you to try.

> misleading marketing around garbage collector
...

> multiple return parameters are overrated
Better than `OUT` paramaters, `errno` or wrapping return types.

> multiple return values have no type checking
False.

> nil as a failure marker
See pointers.

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

> no OOP
Type hierarchies aren't the only way to do OOP. In Go you're working with objects and you compose them far more than in most so-called OOP languages.

> no map/reduce/filter
Never needed them.

> no `this`
No implicit `this`.

> no algebraic data types
Not built-in, no.

> no asserts
Use Testing and runntime checks.

> no constructors
No implicit constructors. Most of the time the zero value will do. When it doesn't `s := NewT()`.

> no decent IDE
GoLand, VSC, editor+shell

> no exceptions
See panic().

> no first-class support of interfaces
What? Yes there is.

> no first-class tuple
Expressions have multiple values and structures are named tuples so who cares?

> no function/operator overloading
Good. Also means you don't need a symbol table. 100k lines a second compilation go brrrrrr.

> no generics
You don't really need them. And they'll be coming soon (TM).

> no immutables
Just don't mutate it.

> no language interoperability (only C)
Fair point.

> no macros or templates
Good.

> no non-nullable types
Structs, ints, floats :slight_smile: . Also, see pointers.

> no pattern matching
See overloading.

> no subpackages
Reminder that C++'s `protected` is an admitted mistake.

> no sugar for slices
There is some.

> no sum types
Use interfaces.

> no semicolons at line endings
Ha!

> no ternary operator
Translation: no hidden control flow/only one way to do things. Good.

> no unused imports
Use the goinports tool before compiling. It'll clean it up.

> no user-type iteration
See `range` (use the clasic `for`)

> no versioning model
Fixed.

> no virtual functions
No inheritance so what'd be the point?

> not stable
I'm not having issues with it. Docker is written in Go and that runs in production a lot.

> not-so-obvious slices behaviour
Depends what you're used to. You learn it.

> package mechanism is broken (2015)
Fixed.

> pointers are a mess
See pointers.

> polymorphism is broken
No. It functions quite well.

> poor design
:clown: 

> poor std math lib
Maybe. I don't have extraordinary math needs, but maybe it's a fair point.

> project layout is bad
Your call.

> psuedointellectual arrogance of Rob Pike and everything he stands for
K.

> questionable compiler rigidity (2015)
Is quite stable. The language switched from C to self-hosted that year so maybe it was new and not very stable at the time, idk.

> slow json parsing
It uses reflect so probably. On the other hand the interface is really really really cool.

> sort.Interface approach is clumsy
Debatable. I don't mind it.

> stuck in 70's
Good. Everything since is shit. \s

> stuck in Unix thinking
Good if you like Unix.

> summary: not elegant as Python, not strong as Java
Python aka "who knows what the data types are" the language?

> “Porting my code review tools to Go from Python. Surprised to see a reduction in line counts.” – Scott Dunlop

> the worst compiler toolchain ever (2014)
Fixed.

> too opinionated
"Do you want everyone to vote for the same political party, too?" - theo

> too simple / lack of syntactic sugar
Good.

> too verbose
Not in practice.

> too young
In 2012.

> type inference is too simple
Good.

> un-googlable name
"Golang".

> unexpected variable shadowing
***Finally a good point***. Yeah, this sucks ass.

> unwieldy to code new collections
See programming without STL.

> upper-case/lower-case scoping is bad
It's expressive. Rip Japanese codebases though.

> weird mascot (gopher)
![Dancing Gopher](https://camo.githubusercontent.com/01ee857f12b45ddd41369434b2954ba463c5b29e/687474703a2f2f7374617469632e76656c76657463616368652e6f72672f70616765732f323031382f30362f31332f70617274792d676f706865722f70617274792d676f706865722e676966)
