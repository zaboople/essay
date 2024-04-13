# Object-Oriented Programming For Beginners, or Why OOP Is Useful

A long time ago I was talking to a very smart and experienced computer programmer who lamented that no matter how much he tried, OOP just didn't make sense to him. This really bothered me: I knew good and well he was capable, so what eluded him? In hindsight it's hard to say for sure; C++ might have been half the problem, but I think he was actually struggling with this question: *Why bother?*

The basic answer is: Scope control. OOP is aesthetically appealing to a lot of programmers, leading to all sorts of nonsensical explanations, but scope control is where it really wins. If you can understand that much, I think you'll "get it".

This is also a detailed explanation for experienced programmers who have misgivings about OOP, maybe because they've put up with bad programming habits from overzealous peers. There *is* value in OOP when it's used sensibly, as explained below.

## Vanilla

We'll start with a language I call "vanilla", which is just an ordinary trifling language that has functions, character strings, integers, floating point numbers, basic arithmetic and structs. We'll give it a compiler and reasonably strict types, so you can't just throw "var" or "def" on everything and expect the world to put up with you; and implicit pointers for simplicity. Vanilla is pretty much what any old fogey would expect of a *non*-OOP language, but we'll slowly upgrade it to minimalist but sufficient OOP capability.

## Private

Suppose we've written a big program in vanilla with 10,000 functions, and notice a problem: It's kind of hard to reason about any given function, because it's hard to reason about its context: What calls this function? I can find out using global search (e.g. recursive `grep`) but that gets old after a while. We might recognize that while all functions are implicitly "global", a lot of them don't *need* to be, so how about we introduce a keyword, "private":

    afile.v:
        private int function foo(string s){....}

This tells the compiler that `foo()` can only be accessed by other functions in the same file (afile.v). This makes life easier by whittling the scope of `foo()` down from "the world" to "just here", to give us an elementary level of scope control. This makes our program a little bit more straightforward, with a minimum of additional complexity.

We could also enhance this concept of *file* privacy to add *directory* privacy, with something like a `dir` keyword, so that our function can only be called from files in the same directory. This is also a useful scope control tactic, but for simplicity I'll leave directory-private out of my examples[<sup>1</sup>](#Footnotes).

## Structs

Suppose we have a struct called "SuperTree", which is some sort of fancy tree thingie, maybe like a binary tree, but "better", and we've written some functions for manipulating it:

    SuperTree.v:
        struct SuperTree {
            int[] indices
            int[] otherIndices
            string[] values
        }
        function void insert(SuperTree tr, string value){...}
        function string[] find(SuperTree tr, string pattern){....}
        function void sort(SuperTree tr, boolean forwardsBackwards){....}

It's worth asking: Since we've already made nice functions for working with SuperTree, does the rest of our program really need to access its variables? Might bad things happen if other programmers do questionable things with those variables owing to misunderstandings about how they work?

What if we brought that "private" thing in here as well:

      struct SuperTree {
          private int[] indices
          private int[] otherIndices
          private string[] values
      }

Same as before: the private things can only be referenced by functions in the same file. Does it make my life easier? In this case, yes, and yet again because of scope control. Some structs are trivial enough that no scope control is needed, but in this case, it makes sense. In fact, it *really* makes sense because mutable state - the stuff hiding in our struct variables - is very tricky, and while some functional-programming obsessives want such outlawed entirely, scope control is a very pragmatic mitigation for the mutable state problem.

Yet again, we've made the program a little bit more straightforward without any serious cognitive overload. We just need one more thing to get to object-oriented programming proper.

## Namespacing

Suppose we are a new programmer on the team, and we encounter SuperTree here:

    RandomFile.v
        function void foo() {
            SuperTree st=new SuperTree;
            insert(st, "hello")
        }

How do we know where to go looking for `insert()`? It might make sense to look first in SuperTree.v, but we have no guarantee. What if there are other insert() functions in our scope, since it's such a common name - will our compiler get confused by that? Let's make one more change to our programming language:

    struct SuperTree {
        private int[] indices
        private int[] otherIndices
        private string[] values
        function void insert(string value){...}
        function string[] find(SuperTree tr, string pattern){....}
        function void sort(SuperTree tr, boolean forwardsBackwards){....}
    }

This last change lets us attach our manipulation functions *to the struct itself*. This gives us a new way to call `insert()`:

            SuperTree st=new SuperTree;
            st.insert(st, "hello")

This `st.insert()` creates a kind of namespacing; when reading this, I know where to look for `insert()` (as does the compiler) because the syntax clearly indicates such. If there are other inserts in scope, we've eliminated scope ambiguity about which one is being used. This is our most complex syntax enhancement so far, but it's still quite reasonable.

When functions are attached to a struct like this, they are often called "methods" instead of "functions", but it doesn't really matter what you call them (some people still say "procedures").

## Final Transition

Finally, I'll add a private function to SuperTree:

    class SuperTree {
        private int[] indices
        private int[] otherIndices
        private string[] values
        function void insert(string value){...}
        function string[] find(SuperTree tr, string pattern){....}
        function void sort(SuperTree tr, boolean forwardsBackwards){....}
        private function void checkInit(){...}
    }

This `checkInit()` method could be used by the "public" methods to verify initialization parameters and so forth. At this point, I'm no longer thinking "private" in terms of "private to this file" but "private to this *class*", both for variables and functions/methods. Yes, I've also switched the keyword `struct` to `class`, just to keep up with the cool kids. In a language like Java programmers usually keep classes and files 1-to-1, but they don't necessarily have to.

Thus we've achieved the essence of OOP. Additional features like constructors, method overloading and inheritance are  worth learning about and using, but less important. If you think of OOP in terms of scope control, things get a lot more straightforward and practical. Of course some things need wide-open scope and there's no point in trying to restrict them; OOP is just a way to limit scope to what is necessary when possible, providing guarantees that make it easier for the reader to think about context. The goal, then, is not to erect pointlessly dogmatic and bureaucratic barriers; that only happens when advocates don't understand *why* their favorite thing is a useful thing.

## Bonus Rant About Bad OOOP

I have a limited knowledge of OOP origins, but the common claim is that things really got rolling with *simulation and modelling*. The purpose of computer simulation is to model some aspect of the world, like, say, weather, or a railroad switchyard, and experiment with it until we can draw some mathematical and/or scientific conclusions. Video games might also fit in, since we are often simulating real-world physics and human situations.

To rehash the time-worn explanation: In a computer simulation, you have entities that *do* things, and these entities can be thought of as *objects* that encompass *state*, as well as *behaviors* that alter and reveal that state. So an "object-oriented" language would let you define a Cow object with "methods" like eat(), wander() and poop() which contain logic defining just how a virtual cow will do these things, and stateful "instance variables" like "stomach" and "location" that those methods alter and reveal. Now you're ready to build a simulation of a cattle farm.

Then we get to the idea of the popular movie "The Matrix" and a fantasy about simulating all of reality in a computer, uploading yourself into a computer, etc., all of which is quite intoxicating. Before long you get programmers who believe The Great Enlightening Insight is that all programming is simulation and that OOP is therefore the most intellectually sophisticated and "right" way of programming because of its affinity for simulation.

The "All Programming is Simulation" thesis made some sense in the mid-20th century as a transitional phase from business via "paper" (and pens, stamps, filing cabinets, etc) to business via computers, where we start by mapping the paper process onto an electronic one. It makes far less sense once that transition is over, and it *is* over. Everyone is computer-literate. You don't have to explain the idea of an "electronic invoice" or an "electronic payment", among other things. While we retain some of those symbolic paper-based artifacts, it's mostly habit: If you want to call a "directory" a "folder", it makes no difference to me and it changes nothing about the subject. Yes, "email" is a lot like postal mail, and I don't mind the carryover. In fact the reuse and mutation of language and symbols is not a 20th-century phenomenon: Human beings have been doing that since we first learned to make words.

In fact the advance of technology today is predicated on our ability to move beyond physical metaphors and use whatever symbolic devices are handy, i.e. the existing computational abstractions that we already have. However, this shift does not render OOP moot, because modelling & simulation was never the great enlightening insight of OOP anyhow; again, that insight is *scope control*.

Perhaps an example of the flaws of the "everything is simulation" approach would help here: Once I was working on a little homemade video game which required a virtual deck of cards, and of course the deck needed shuffling, and I thought, well, what *is* the most efficient way to do that? Turns out this problem was solved [in the 1960's](https://en.wikipedia.org/wiki/Fisher%E2%80%93Yates_shuffle): You can do the shuffle *in place* with a single plain-jane array in about 6 or 7 lines of your favorite programming language, if you have a good-quality random() function. The algorithm works out to O(N) and uses minimal extra memory for a handful of temp variables.

However: Research is hard, so before landing on the *easy* solution, I came upon a message board for C++ and Object Pascal programmers, where this same question came up and inspired a lengthy discussion. The group quickly posited: Well, if I put an actual deck of cards in front of you right now - sans computer - how would you shuffle it? Thus ensued much discussion about the "riffle shuffle", "cutting the deck" and various physical mechanics. Eventually someone pointed out that mathematicians proved you need at least 7 consecutive riffle shuffles to obtain a proper random sort. The resulting solution would be at least hundreds of lines, inefficient and error-prone, all thanks to the all-programming-is-simulation heuristic. Meanwhile, the easy solution had been available for decades. This is how we regress rather than progress.

Put simply, I don't care about simulation and modelling unless I'm involved in work that actually requires such. OOP would be a lot more useful if everyone stopped trying to force this fantasy onto every project they encounter, but that may take a few more decades.

#### Footnotes

<sup>1</sup> One of my biggest complaints about Google Go is that it *only* has directory privacy; there is no concept of file-private! Most Java programmers are unaware of directory-private, or don't care, and to be honest, if I had to pick *only one*, file-private is more important. Either way, it honestly makes a lot of sense to have both, and it's dissapointing that Google's idea of progress is, well, regressive - back to that theme again...

----

[Back to Software main page](./README.md)
