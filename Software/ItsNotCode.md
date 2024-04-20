# It's Not "Code"

*Note: This essay is partly based on the essential thesis of an academic paper written in the 1990's, I think, but I can't find the darned thing - if you can, drop me a line and I'll be happy to cite it.*

Engineers build descriptions of systems, usually with lots of drawings. Construction workers build those systems - maybe on a dirt lot, maybe in a factory, often with engineers nearby but generally hands-off. It's easy to recognize the difference between design and construction because the physical nature of the work is so readily apparent.

When it comes to computer programming, we could ask: Where is the design, and where is the construction? There is no construction site, hard hats, factory floor, or anything like that. If software engineers are engineers, shouldn't they be able to clearly delineate which parts are which? Is there any design at all? Are they just slapping things together without a plan, like a construction worker who just nails things together until something mildly useful-looking appears?

Consider a second, seemingly unrelated question: What do we mean by "code"? Computer programmers talk about "code" and "coding" (really, _encoding_) and accept this terminology without question. This is actually where all of our trouble begins, especially the trouble of separating "design" vs. "construction".

## Minor History

Most software engineers work in programming languages, not the actual machine language itself (e.g. the x86 instruction set). A compiler or interpreter program _automatically_ does the job of translating the programming language statements into machine language, usually in just a few moments on modern computer hardware. As a result, many computer programmers don't understand machine language because they don't _need_ to, and they often don't understand how the machine even works; they may be aware that the machine doesn't know what common programming idioms like a "function", a "class", a "for-loop", etc. actually *are*, although most computer science curriculums at universities have one or more courses on computer architecture, compiler design, etc, which can help a lot. Learning assembly language is a pretty good shortcut, if nothing else.

The first computers didn't have programming languages. Programmers worked in numeric machine code directly, and it was very painstaking work at that. In fact it was designated as "women's work" akin to typing, sewing, and such, and given to the ladies so that men could focus on the business of making clever computer hardware. Almost all of the first programmers were women (Ada Lovelace is usually credited as the very first), until the men apparently realized that the ladies were having too much fun.

## The Birth of Software "Engineering"

Even after the advent of programming languages, various critics watched the programmers at work, and pointed out that the programmers were engaged in "construction" without any kind of diagrams or blueprints. Extending the engineering analogy from other trades to programming, the finger of accusation declared: _Aha - Construction without design!_ This worked out nicely for the critics, who happily volunteered to take charge of this design problem and rectify it - for a hefty fee, of course, since proper engineers often earn more than construction workers. Some elevated themselves to the position of _architect_, the grandest engineer of all, so to speak. Then they set about the task of making _diagrams_.

This resulted in many, many pages of diagrams needed to describe a few pages of programming language text, which made for a lot of work, because drawing things takes a lot of time. Worse yet, the act of drawing didn't actually do anything to ensure the program would work, which meant that upon testing there was quite a bit of "Back to the drawing board," as they say. The more advanced computer-aided-design tools employed an idea of "round-trip engineering", where the drawing could automatically translate to the intended programming language statements and back - well, most of the time, sort of...

Eventually it became apparent that the "design" work was eating up a lot of time and money, but the results weren't any better than before. Nobody was sure why, but most folks quietly abandoned the idea and went off to chase other fads. Nowadays the only thing approaching a formal "drawing" that you'd see is usually whiteboard scribbles with a caption of, "Do not erase!" even though nobody understands them. Eventually someone erases them when we run out of whiteboard space, or at least tries to, because the whiteboard marker has bonded to the surface permanently. Yes, drawing software exists, but apparently it fails to achieve the desired sense of spontaneity.

In any kind of engineering that *isn't* software engineering, everything is fundamentally about physics, and physical systems lend themselves well to drawings and diagrams. Software has no physics! It exists entirely in the symbolic realm, so would it make sense that a proper software design be a strictly *symbolic* design?

## No, It Really Isn't Code

This whole diagramming epic failed because the design was *already being done* by the programmers, in a different diagramming "language": *A programming language*.

So about this "code": When programmers talk about working in a programming language, they insist on using the term, "code", as in "I wrote some code for that!" or "Well, I've been busy coding all day, what have you been doing?"

What do most people think of when you say, "code"? They think of "gibberish" - something hard to read, even encrypted. They figure that to read this gibberish you need to be a kind of savant, nearly a human computer, and can look at something like

    01100011011100100110000101110000

and explain it without hesitation. Some programmers can do that, but that's because they work all day long on low-level machine code, colloquially known as "bare metal", and they still need some kind of context including processor model, operating system, etc. Again, however, that's not the kind of work most of us are doing, and it's a very slow, tedious sort of work. Instead, most of us do:

    if (x==y)
        println("They matched");

We can rightfully describe the act of speaking or writing in English, Russian, Sanskrit etc. as *encoding* - listening or reading as *decoding* - but that's not how most of us actually talk about it. Programming computers using a language like Pascal or C or Simula is mostly the same. It's more rigorous and unambiguous, and less anarchistic and organic for sure, but it's descriptive _language_, not code. We are not encoding machine language, or even thinking of the machine; we are working on an abstract description, an idea that exists purely in a conceptual space. It's quite an accomplishment that we can work at such an extreme level of abstraction. This is especially true in the world of business information systems, where most of us work and where the end goal is the operation of our business, not the operation of our computer, which is just a means to an end.

Before we start bickering and splitting hairs about just what is "code" or isn't, however, maybe we ought to state the *goal* of such arguing: Describe what you do for a living to people who don't understand, and describe it intuitively, or they'll start making silly diagrams and ordering you around like a pipefitter, tailor or restaurant line worker (noble professions all, but not engineering).

## Who Wears the Hard Hat?

Things would be lot easier if we had some burly folk in flannel shirts, nailing up the 1's and 0's, installing a spigot here and there, maybe some gauges and things, something management could look at and say, "Well it's coming along nicely, I must say!" Then they'd look over at you, a *software engineer*, and ask, "Who the heck are you?" and you could say, "Well, I'm the engineer who designed this!" Then they would say, "Oh! Well then, fine job you've done here. Looking good!"

But we don't do that. The construction work is actually done by compilers and interpreters - yes, that's our tradespeople. They're invisible and extraordinarily quick, so much so that everyone forgets about them and decides that the computer programmer is the tradesperson.

Grace Hopper described programming as "writing a cookbook", which is pretty close. Progammers write the recipe, but we don't cook the dish. Certainly it helps to have some hands-on experience, doing the dirty work of compilers all by one's self without assistance; but you're going to spend most of a career in computer programming writing the cookbooks.

In the end, I don't write computer programs; I write *descriptions* of computer programs. The compiler/interpreter does the rest. Programming languages are in fact *design* languages, and programmers are designers. Again, there is no physics, only the symbolic realm, and it turns out that symbolic language is the ideal design tool, not diagrams. Just because someone decided to describe the work as "software engineering" doesn't make diagrams a good idea.

## Is It Really Engineering?

It actually does make some sense to think of computer programming as engineering, in the sense that we need to build working systems derived from mathematic and scientific discovery, but the academic domain is computer science rather than physics and its specializations. As with any other engineering, our working systems need to be reliable, consistent, robust, fault-tolerant, etc., and especially *safe* - effectively, they need to be *ethical*. Our collective ethical neglect has become a larger and larger problem over the last few decades as computing spreads to all possible domains, and as people's lives and livelihoods come to depend on the quality of that computing.

It has been difficult to convince computer programmers to mature and discipline themselves instead of wallowing in a tough-guy, danger-close, hacker-cowboy mentality of arrogance justified by a constant refrain of, "How else am I supposed to innovate?" That's actually a good enough question to insist on learning to manage risk effectively rather than refusing to accept risk at all, but room for improvement abounds.

## Distributed Systems

Now that everyone is obsessed with building extraordinarily distributed systems with dozens of network nodes, it *does* make sense to start diagramming those networks, since they are specifically *physical* entities. Unfortunately most of us still rely on word-of-mouth, whiteboard photographs and institutional memory to keep track of these mostly invisible diagrams, often with lots of mistakes and missing parts. If you force a typical software development team to draw a reasonably complete diagram "or else", you can usually guess the diagram's accuracy by how much it embarrasses the team with its organically grown, mind-boggling complexity.

## Impact

Estimating a construction project is going to be a lot easier with blueprints in hand, but how do you estimate it before then? Very, very carefully! This is why software project estimation is so difficult, because you're *designing as you go*! The design and the construction are done at the same time. You're not even sure your design will work. In fact, you can usually estimate construction at "anywhere from a few seconds to a few minutes", which sounds great until everyone realizes the design will take days, weeks, even months. It might help management understand computer programmers better if we explain things this way.

A lot of software development managers want to have two tiers of software development, where a smaller, higher-paid group does all the thinking - architects and other senior people - and a larger, lower-paid group does all the "labor". They are trying to imagine the typical computer programmer as a laborer, albeit a skilled laborer. The image of "coder" assumes you are using your savant-like skills to tediously grind out all those 1's and 0's, and you need to shut up, focus on grinding, and especially improve your grinding speed. It's not the loss of status here that offends, but a complete misapprehension of what the job is about.

So, maybe stop saying "code" when you talk about programming computers.

----

[Back to Software main page](./README.md)
