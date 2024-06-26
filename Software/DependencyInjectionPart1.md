# Dependency Injection Part 1: For The Uninitiated

You might think of Dependency Injection as a highly sophisticated "design pattern" only understood by elite, highly educated software engineers, out of reach for ordinary hacks. You might even think that DI is so fancy as to be of little pragmatic value - merely another way for snobs to show off. It's actually just ordinary common sense, easily understood by any mediocre programmer. If you also understand object-oriented programming, you have more options, but you don't need to be much of an expert at OOP. In fact, you might find yourself thinking, "I already do that, but I never thought it needed to have a special *name*..."

Part 1 - this part - is for people who don't understand DI at all, or who don't know that they understand it, or that thought they understood it but were making it overcomplicated. Part 2 is about frameworks that make DI overcomplicated.

## It Was Just A Simple Function...

The place to start is a simple function like:

    mine()

Suppose I've told you to use this function of mine, because it will solve all of your problems, so you do. This leads to a one-act play in three scenes:

Scene 1: *It blew up*

    You: That function you said would solve all of my problems? It blew up.
    Me: What? That's impossible
    You: Nothing is impossible.
    Me: Let me look

Scene 2: *You forgot*

    Me: Well I see the problem: You forgot to initialize the global variable!
    You: "Forgot"?
    Me: Yes, it's named "a". See? You have to have something in there!

Scene 3: *Come to think of it*

    You: Well, I initialized the "a" variable this time, and it still blew up.
    Me: Hmph. Let me look again...
    Me: Oh! You forgot to initialize the _other_ global variable, named "b"! See?
    You: [Sighs deeply]
    Me: Come to think of it, I should probably tell you about "c"...

After some trial and error, we determine 3 different global variables require initialization: a, b & c. So maybe you could try to convince me to write `mine()` like this instead:

    mine(a, b, c)

This way, my function explicitly declares its dependencies, and nobody has to guess about global variables. This is actually a legitimate thumbs-up DI implementation, but don't tell anybody I said that.

Our problem is _hidden dependencies_. This is only one bad thing about global variables, but it's a very good reason to get rid of them. If you can appreciate the idea of reducing global variable usage, the rest is really just a matter of following your nose.

## Global Functions

Sometimes people "fix" global variables by hiding them behind global functions, like this Java example:

    Globals {
        private static String a, b, c;
        public static setA(String aa) {
            a=aa;
        }
        //... same for b & c
    }

That isn't going to help our problem very much, and it's why you may see the term "static singleton" used in association with Java instead of "global variable", to thwart the would-be cheaters, so to speak.

## OOP DI

Our one-act play sprouts a new scene:

Scene 5: *Terrible, of course*

    Me: Your idea of mine(a,b,c) is terrible, of course.
    You: Why?
    Me: I have a whole bunch of functions

        mine1()
        mine2()
        ...
        mineX()

    Me: All of those use a, b and c. By your reasoning, I should rewrite _all_ of them, and do you
      have any idea how long that would take, and how cluttered everything would be if I did it _your_ way?

You might suggest a way to mitigate this, by creating a "struct" that encloses a, b & c:

    ABC abc = ABC(a, b, c)
    mine1(abc)
    mine2(abc)

After all, one parameter is easier than three. You can also suggest the object-oriented solution:

    Mine m = Mine(a, b, c)
    m.mine1()
    m.mine2()

That's very fancy, and it does eliminate all the parameter-passing except for the one-time constructor parameters. However, all of three of our solutions soon lead to a new problem.

## Call Chains

Our function calls don't exist in isolation, but as part of a larger program, something like:

    main(args) -> his() -> hers() -> yours() -> mine(a, b, c)

The reason a, b & c were global is because they likely *need* to be shared - a complete diagram might show dozens more functions using them. They are probably going to end up being initialized by `main()`. So where is `yours()` supposed to get a/b/c from, before passing it to `mine()`? It would be hypocritical to resort to global variables now, so:

    1) main() -> his(a, b, c) -> hers(a, b, c) -> yours(a, b, c) -> mine(a, b, c)

Here, `main()` is our program's entry point, and a very reasonable place to initialize a, b & c since main is at the root of the call stack and all its divergent paths. But now _everyone_ is complaining about Too Many Darned Parameters, so we circle back to some of our previous ideas, starting with the ABC(a,b,c) solution:

    2) main() -> his(ABC(a,b,c)) -> hers(abc) -> yours(abc) -> mine(abc)

Note that I'm using `ABC` as a class/struct name and `abc` as an *instance* name in example 2.

Anyhow, here's the basic object-oriented solution, but it isn't much different:

    3) main() -> his(Mine(a,b,c)) -> hers(mine) -> yours(mine) -> mine.mine()

Or maybe we bring down the OOP hammer globally, so to speak:

    4) main() -> His(Hers(Yours(Mine(a, b, c)))).his() -> hers.hers() -> yours.yours() -> mine.mine()

We've banished all "static" methods, and told everyone: From now on everything will be 100% OOP. So, each function is now part of a class: `His.his()`, `Hers.hers()`, `Yours.yours()`, `Mine.mine()`; and each class takes an instance of another in its constructor.

Example 4 is a liiittle bit contrived (one function per class? I've seen it before) but the noteworthy point is that now, nobody has to deal with indirect, AKA "transitive" dependencies, only the ones that matter to them directly, and that makes it somewhat easier for readers to determine what *really* uses a, b & c - the same old `mine()` function. From the standpoint of dependencies and scope control, it does well, but the downside is some heavy-handed OOP juggling. All in all, this option is at least *acceptable*.

As an exercise for the reader, in this specific example you could probably wangle an object-free functional-programming solution with currying and lambdas. Also, if I didn't point that out, the FP people would come looking for me (I know you're out there...).

## The Big Picture of Global Variables

If you're offended because you don't see why we have to be so militant about eliminating globals, I'll agree that globals aren't automatically the end of the world as we know it (that's a pun, ha-ha... sigh...). The smaller the program, the smaller the chance of a global-induced disaster. Think of it this way: Is a 10,000-line OOP class with instance variables better or worse than a 5,000-line "object-less" program with global variables? What if it's a 5000-line program where *everything* is all in one _function_?

Object-oriented "instance" variables are really "mini-globals". They don't solve a problem so much as _mitigate_ it down to a manageable level, by reducing "global" to "not as global". The same concept applies to "stack variables", i.e. where the lifespan is isolated to a function. Overall, the goal is to reduce the _scope_ of "mutable" (changeable) state, ideally down to only the logic that requires it and no further, depending on what tools we have at our disposal.

We might even consider _time_: Temporary state is a lot easier to deal with than state that exists for the lifetime of the program, and even worse, state accessed by different "threads" (ugh, multithreading!) of execution simultaneously. More on that in part 2...

One more question: Once the program is actually converted to machine instructions, can the computer's CPU tell the difference between global-vs-not-as-global? In all cases I know of, no. We pursue our goal for the sake of _people_, not the computer itself, which could care less. As people, we want guarantees about what we're reading so we can understand it better.

[Dependency Injection Part 2: A Good Idea Becomes a Huge Mess](./DependencyInjectionPart2.md)

----

[Back to Software main page](./README.md)
