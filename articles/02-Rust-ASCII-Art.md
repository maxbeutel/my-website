Rust ASCII Art library - the first approach
==============

In order to try the current state of things in Rust, I decided I want to hack a little program in Rust, nothing too complicated. Inspired by a great [Haskell tutorial](http://www.seas.upenn.edu/~cis194/fall16/), that doesn't start with a recursive implementation of fibonacci sequence, but with something actually interesting, like drawing images, I thought I could start with something similar. A bit less ambitious though, just drawing ASCII art on the terminal.

Turns out, I underestimated the whole thing a bit. For the first two approaches i basically just hacked away, to find that in the end I programmed myself into a corner and should start over again.

The third iteration now also contains tests, which helped a lot guiding how an API should look like and what I actually want to achieve.

First approach - doesn't even compile properly
-----

My first approach was relatively straight forward, with different types for `Circle`, `Line`, `Canvas` etc. which should be able to be combined and then be outputted on the screen.

In the end I tried to dumb down the program to be able to debug it, because it caused issues with combining shapes - some of the coordinates should actually be drawn as a ``Line``, but showed up empty (as a `Canvas`), apart from that I also created some weird workarounds to fight Rusts quite aggressvive borrow checker.

As there are no interfaces in Rust, I tried to implement a common trait for all `Shape`s, the trait is called `Plottable`. This introduced another set of errors, as it's not possible to just put a trait into a collection in Rust, because a trait doesn't have a size known to the compiler, therefore it doesn't know how much space to allocate e. g. for a `Vec&lt;Plottable&gt;`. So I needed to `Box&lt;Plottable&gt;`, which causes API changes and more type warnings all over the place.

In the end, this showed that the design just wasn't mature enough:

* In some places a `HashSet&lt;Coordinate&gt;` is used to represent a unique collection of coordinates (because a coordination system shouldn't condtain duplicate coordinates, like two times `(x: 0, y: 0)` getting plotted on a 2D x/y graph would be weird. I still think this is a useful constraint, but the code didn't communicate that clearly.
* Because in other places, a `Vec&lt;Box&lt;Plottable&gt;&gt;` is used, which is a collection of Plottable objects. So I can combine a `Circle` and a `Line` and get a `Canvas` back. Which sounded quite good in my mind, but doesn't really make sense. Why is `Line` + `Circle` = `Canvas`?
* I didn't think hard enough about ownership, so I had to introduce a lifetime parameter for the `Canvas` and the Plottable's it contains. Lifetime parameters are completely valid in Rust, although in  my personal opinion they seem a bit clunky and therefore I would rather avoid them if it's not 100% needed.
* The combining of shapes was buggy, combining a `Canvas` that contains already some other Shapes with another Shape would get objects deeper down int he object graph "overwritten" in the output, meaning the wrong point was rendered and it wasn't clear why.

So all in all: the [first approach](https://github.com/maxbeutel/rust-ascii-art/blob/9908fe3e1afc74264065a8e2188f759fed769816/src/main.rs) failed, back to the drawing board.
