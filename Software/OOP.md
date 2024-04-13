# OOP For Beginners, or, Why OOP Is Useful

A long time ago I was talking to a very smart and experienced computer programmer who lamented to me that no matter how much he tried, OOP just didn't make sense to him, and this really bothered me: I knew good and well he was capable, so what eluded him? It's hard to say for sure, but I think he was actually struggling with this question: *Why bother?*

The basic answer is: Scope control. OOP is aesthetically appealing to a lot of programmers, leading to all sorts of nonsensical explanations, but scope control is where it really wins. If you can understand that much, I think you'll "get it" - although unfortunately I never tried this with my friend from a long time ago.

This is also meant as a detailed explanation for experienced programmers who have had misgivings about OOP, largely because they've put up with bad programming habits from peers. The message here is that there *is* value in OOP when it's used sensibly, as explained below.

## Vanilla

We'll start with a language I call "vanilla", which is just an ordinary trifling language that has functions, character strings, integers, floating point numbers, and structs. We'll give it a compiler and reasonably strict types, so you can't just throw "var" or "def" on everything and expect the world to put up with you. Vanilla is pretty much what any old fogey would expect (we can give it implicit and/or explicit pointers, but that's not the most important thing).

## Private

Suppose we've written a program in vanilla, and it has 10,000 functions, and noticed a problem: It's kind of hard to reason about any given function, because it's hard to reason about its context: What calls this function? I can find out using global search (e.g. recursive grep) but that gets old after a while. We might recognize that while all functions are implicitly "global", a lot of them don't *need* to be, so how about we introduce a keyword, "private":

    afile.v:
        private int function foo(string s){....}

This tells the compiler that that foo() can only be accessed by other functions in the same file (afile.v). How is this going to make my life easier? By whittling the scope of foo() down from "the world" to "just here". I've given us an elementary level of *scope control*. This makes our program a little bit more straightforward, with a minimum of additional complexity.

We could also enhance this to add *directory* privacy as a keyword, so that our function can only be called from files in the same directory; in Java this is called "default" access because it's assigned by the *absence* of a keyword (confusing, no?). For simplicity, however, I'll leave this out of my examples.

## Structs

Suppose we have a struct we call SuperTree, which is some sort of fancy tree thingie, maybe like a binary tree, but "better", and we've written some functions for manipulating it:

    SuperTree.v:
        struct SuperTree {
            int[] indices
            int[] otherIndices
            string[] values
            //etc...
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
          //etc...
      }

Same as before: the private things can only be referenced by functions in the same file. Does it make my life easier? In this case, yes, and yet again because of scope control. Some structs are trivial enough that no scope control is needed, but in this case, it makes sense.

Yet again, we've made the program a little bit more straightforward without any serious cognitive overload. We just need one more thing to get to object-oriented programming proper.

## Namespacing

Suppose we are a new programmer on the team, and we encounter SuperTree here:

    RandomFile.v
        function void foo() {
            SuperTree st=new SuperTree;
            insert(st, "hello")
        }

How do we know where to go looking for `insert()`? It would make sense to put insert() in the same file as SuperTree, but is it there? What if there are other insert() functions in our scope, since it's such a common name - will our compiler get confused by that?

Let us suggest one more change to our vanilla programming language:

      struct SuperTree {
          private int[] indices
          private int[] otherIndices
          private string[] values
          function void insert(string value){...}
      }

This last change attaches an `insert()` function to the struct itself, allowing a new way of calling the function:

        SuperTree st=new SuperTree;
        st.insert(st, "hello")

This `st.insert()` creates a kind of namespacing; when reading this, I know where to look for `insert()` (as does the compiler) because the syntax clearly indicates that it is part of the SuperTree struct itself. If there are other inserts in scope, we've eliminated scope ambiguity about which one is being used. This is our most complex syntax enhancement so far, but it's still quite reasonable.

Now we can call SuperTree a "class" and `insert()` a "method" as per object-oriented parlance, and we've achieved the bare minimum and essence of OOP. We could also use `private` on some of these methods to enforce their scope down to the class itself, which is a common practice; for example we might have a `checkInit()` method that verifies certain initialization parameters, called by only some of the other methods in our class.

Additional features like method overloading and inheritance are interesting and worth having, but less important.

## Bonus Rant About Bad OOOP

I didn't invent object-oriented programming myself, so I have a limited understanding of its origins, but the common claim is that it really started with simulation and "modeling". The purpose of computer simulation is to model some aspect of the world and play with it until we can draw some mathematical and/or scientific conclusions - or an approximation thereof, at least.

I'll rehash the time-worn explanation: In a computer simulation, you have entities that *do* things, and these entities can be thought of as *objects* that encompass *state*, as well as *behaviors* that alter and reveal that state. So an "object-oriented" language would let you define a Cow object with "methods" like eat(), wander() and poop() which contain logic defining just how the cow will do these things, and state variables like "stomach" and "location" that those methods alter and reveal. Now you're ready to build a simulation of a cattle farm, or something like that. This is how I'm told OOP began, and I suppose it makes sense.

Then we get to the idea of the popular movie "The Matrix" and a fantasy about simulating all of reality in a computer, uploading yourself into a computer, etc., all of which is quite intoxicating. Before long you get programmers who believe The Great Insight is that all programming is simulation and that OOP is therefore the most intellectually sophisticated and "right" way of programming because of its affinity for simulation.

The "All Programming is Simulation" thesis made some sense in the mid-20th century as a transitional phase from business via "paper" (and pens, stamps, filing cabinets, etc) to business via computers, where we start by mapping the paper process onto an electronic one. It makes far less sense once that transition is over, and it *is* over. Everyone is computer-literate. You don't have to explain the idea of an "electronic invoice" or an "electronic payment", among other things. While we retain some of those paper artifacts, it's mostly habit: If you want to call a "directory" a "folder", it makes no difference to me and it changes nothing about the subject. Yes, "email" is a lot like postal mail, and I don't mind the carryover of icons. In fact the reuse and mutation of language and symbols is not a 20th-century phenonmenon: Human beings have been doing that since we first learned to make words.

In fact the advance of technology today is arguably predicated on our ability to move beyond physical metaphors and use whatever intellectual devices are handy - perhaps even the existing computational abstractions that we already have. However, this shift does not render OOP moot, because modelling & simulation was never the key insight of OOP anyhow.

Perhaps an example of the flaws of the "everything is simulation" approach would help here: Once I was working on a little homemade video game which required a virtual deck of cards, and of course the deck needed shuffling, and I thought, well, what *is* the most efficient way to do that shuffling operation?

Turns out Donald Knuth solved this problem in the 1960's: You can do the shuffle with a single array in about 6 or 7 lines of your favorite programming language, if you have a reasonably effective random() function. The algorithm works out to O(N) and uses no extra memory or linked lists.

However: Research is hard, so before landing on Mr Knuth's solution, I came upon a message board for C++ and Object Pascal programmers, where this same question came up and inspired a lengthy discussion. The group quickly posited: Well, if I put an actual deck of cards in front of you right now - sans computer - how would you shuffle it? Thus ensued much discussion about the "riffle shuffle" and "cutting the deck" and various physical mechanics that come into play. Eventually someone pointed out that mathematicians proved you need at least 7 consecutive riffle shuffles to obtain a proper random sort. Before long it was apparent that the solution would be at least hundreds of lines and highly error prone, all thanks to the all-programming-is-simulation heuristic.

Put simply, I don't care about simulation and modelling unless I'm involved in work that actually requires such. OOP would be a lot more useful if everyone stopped trying to force this ideal onto every project they encounter, but that may take a few more decades.

----

[Back to Software main page](./README.md)
