# Dependencies Are The Great Problem of Software Engineering

Dependencies are a fundamental problem that is killing software engineering teams everywhere, largely due to a complete lack of awareness, much less actual interest in the problem. Computer Science curriculums do little to address it and probably shouldn't attempt to take sole responsibility for it; it's really part of the mostly ambiguous, nebulous, ill-defined and emergent discipline of "software engineering".

## The Lifecycle of Doom

Suppose you're working on a brand new project, "green-field" and all that. Your biggest problem is that you need to build a team to work on it. How are you going to find enough qualified people? Easy, just make a job advertisement that says:

  *Hiring for brand-new starting-from-scratch project.*

The world will beat a path to your door.

Your new team will be considered the best and brightest team. People will brag about how fast things get done, how "on the ball" everyone is. They'll remark how the old team took *forever* to get anything done, and most of the time they didn't get anything done at all! What's the new team's secret? Maybe they're just the smartest, most educated, hardest-working people you ever met, is all. Maybe nothing of the sort...

Wait about five years, however, and the grumbling begins: What's the problem with those guys? They used to be the A-team, now they're the D-team. All they do is shrug, complain and make excuses. Why can't they get anything done?

Maybe the problem is *attitude*, so threats are made and carried out, staff fired & replaced. Hiring is harder than before, though, because it's the kind of work nobody wants to do, not to mention the least respected: legacy/maintenance. The people you hire will be people you previously passed over. The original staff, having proven their value as the smartest and best have already left to go to other companies who want the smartest and best to help start new projects that eventually end up the same. The cycle repeats itself.

## Why?

Originally, the team was unencumbered, and so there was little to fear when working on new ideas. You can't break anything if there's nothing to break, and only a few things if there are few things to break. Five years later, that isn't true anymore.

*Dependencies* between various parts of the architecture caused unintended effects in unexpected places. Changing one thing breaks 2 other things, and fixing those breaks 4. Most of the team's time is spent figuring out how *not* to break things, and how to fight the fires that result from things breaking when people guess wrong.

The typical strategy of our aging team is avoidance: Change as little as possible. The tooling and technology becomes more and more outdated, making it even harder for the team to be competitive with newer teams and hire new staff. Large-scale upgrades are often the last straw: A major change is attempted, but the implementation fails in the face of so many complicated dependencies, or worse yet, it's a boondoggle that solves nothing even if it succeeds. This is often when the Great Security Debacle happens: A critical security patch is never applied, data is stolen, etc., and the organization's reputation is ruined.

You might recite the truism, "*Complexity* is the fundamental problem of software engineering!" This is like saying, "Life is complicated!" Duh. It isn't entirely wrong, and after all, it's the *complicated* dependencies that will kill you, but *dependencies* are the fundamental problem, and they're suffocating teams to death. Thus the more insightful statement is, "Dependencies are the great problem of software engineering." Put simply, *dependencies kill development teams*. It is a slow death, where the team gradually struggles more and more to enable change while ever-expanding dependencies weigh them down, until finally, they are immobilized, and for all intents and purposes dead.

One might think we'd improved on this problem over the decades, but we've gone the opposite direction. Once upon a time, everyone wanted to be chief architect, and everyone was frustrated, because to be chief architect, you need complex architecture, and that used to cost a lot of money. Cheap infrastructure and open source are good things, but they also made hyperarchitecture possible. Just using free software you can create systems that are incomprehensibly complex, but more importantly, that have dependencies on hundreds of libraries, drivers, protocols and more that have to be kept up to date, compatible with each other, and secure. We can even extend the dependencies across network nodes, for extra horror.

Programmers often get angry at problems without perfect solutions, but in this case, there is no perfect solution - only mitigations. Our ideal outcome is to *reduce* dependencies, and barring that, to organize and *illuminate* them so that we know what we have to deal with and manage.

## The Scope of the Problem

For the sake of perspective let's enumerate the common ways in which dependencies plague us, and some mitigation strategies:

### Internal Logic

This is just the dependencies stretching across the internals of a single application, between variables and functions. This calls that calls the other calls another. Change something over here, break something over there.

Programmers sometimes hear, "global variables are bad" but seem to have trouble understanding *why*, because nobody ever tells them. Global variables are bad because it's difficult to determine the extent of their effects; potentially *everything* could have a dependency on a global variable.

Object-oriented programming has unfortunately fallen out of favor in some circles, largely because it was overhyped in the first place as a magical thing of indeterminate metaphysical wonder (something about modeling and simulation). The *biggest* benefit is *scope control*. OOP allows you to control the scope of variables, functions, and functions acting on variables, so that you can *clarify* dependencies - but no, not *reduce* them.

While a lot of modern teams prefer scripting languages like JavaScript and Python, compiled languages do make it easier to keep track of internal dependency issues, as well as library dependencies. Any kind of "linter" that can do this work in a scripting environment can be a valuable substitute.

### Database

Databases typically have anywhere from thousands to trillions of global variables. You might vehemently resist this "global variable" label as unfair, but consider that once an application has access to a database, it can usually access any record in any table in that database, often both reading & writing. Each row-column value acts as its own global variable. Worse yet, analysis reveals that the database is even *more* global than you think:

1. If multiple applications have access to the database, our global variables affect multiple applications at the same time, unlike our usual globals.

2. If a global variable becomes corrupted in an application, a restart should fix it. Furthermore, it won't affect other applications. What if data becomes corrupted in the database - will a restart help? No. Worse yet, any other application instance - a copy or something different - reading from that database will be corrupted too, and stay corrupted (at least they'll be consistent).

3. How do you know which records the application is accessing? Normally a compiler (or even a linter) can help you identify accesses of global variables. When everything is done with dynamic SQL and other languages, a compiler can't help you and complain when you reference a table or column that isn't there; a linter can't help you track references for analysis. Your compiled language is forced to behave like a scripting language.

If you've recently put in a significant effort to do away with dozens of global variables in your application, it might be agony to discover that your database is adding millions more, but that's what databases do. We can only mitigate this, of course, since databases are absolutely necessary:

1. Avoid having different applications connect to the same database, especially in read-write mode.

2. Within an application we should do our best to isolate database logic so as to limit our search space when looking for clues about what comes from where.

3. We might also reconsider table names, given that we only have search functionality to track down dependencies, and names like "person" or "location" are going to give dozens, even hundreds of false positives; consider adding a special prefix like "tbl" to names, even though this will infuriate some staff (surprisingly, experience reveals that 3 extra letters won't kill you).

4. If a second application needs access to your database for whatever reason - and in a hurry - consider giving them a dedicated database user account restricted to dedicated database *views* rather than direct access to tables.

### External Libraries

Many programmers think 3rd-party libraries are a solved problem: Add the library name to a magic build file, and not only is the library automatically downloaded, but all of its own "transitive dependencies" as well! This is doing less to mitigate or solve our dependency problem than to *hide* it. New dependencies can be added almost imperceptibly; approval & authorization is usually seen as unnecessary.

What about transitive dependency conflicts between different libraries? What about security updates? What about abandonware? Some generalities to pay attention to:

1. Avoid adding unnecessary dependencies that we can afford to do without. We need better reasons than a library being irresistibly clever and magical, or just trendy.

2. Be careful about libraries that threaten to fall into abandonment, especially owing to lack of contributors. This threat often changes over time, with today's hottest fashions often becoming the future's fashion faux pas.

3. Where possible, seek out curated library collections where commonly used tools have been tested together and verified for compatibility.

4. If you are an open source author, and you've realized that your library needs a backwards-incompatible overhaul, *you need to rename your library!* If you follow this practice properly, users will usually be able to make use of both old and new without excessive conflicts, and transition gradually instead of doing a big-bang all-hands-on-deck conversion and risky rollout.

### Internal Shared Libraries

When the Java programming language was first released, it was promoted by Sun Microsystems as a magical fountain of reuse. Somehow OOP was going to make everything way more reuseable than ever before, with everybody pitching in. This mentality was delusional but a lot of us bought in on it. Writing reuseable libraries is actually really hard because of the need to anticipate unknown requirements, and not even OOP fixes that.

The first thing to understand is an implicit falsehood, that "something is better than nothing". A shared library is not guaranteed to be 100% asset-only and liability-free. The more applications that depend on your shared library, the more there is to break when you try to make change, and the more retrofits you have to do when failure to anticipate requirements results in a backwards-incompatible rewrite. Some software developers decide to avoid the problem by simply refusing to make their library flexible enough, forcing others to reject stakeholder requirements in turn.

Programmers often write their corporate shared libraries as wrappers around different open-source libraries, creating a transitive dependency issue. *Don't* write thin, pointless wrappers around open-source just for the sake of your ego (yes, that is definitely a thing).

Be careful about using your corporate rank to force your library on other software developers. While there might be some need to have an "official way" to do certain things, that excuse is sometimes used where it isn't valid. When others don't have the *choice* to use your idea of a *better* way, you lose the competitive motivation to improve that idea.

Know your domain: The best way to anticipate future requirements is to have seen them before. The more experience you have in the domain you are writing a library for, the more likely you are to succeed.

You might want to consult with Jeff Atwood's rule-of-threes essay(s) on this topic. His estimate is that writing shared libraries is about 10x harder than the usual application-specific programming, and that matches my own experience. Not to imply that it's the rarest of skillsets, but honestly, most of us just aren't disciplined, experienced and energetic enough to see the shared library job through properly.

The easiest way to get good results from your shared library effort is to limit its scope and focus on a single specific, clearly delineated problem. If successful, the experience will leave you much better prepared for more ambitiously scoped libraries later on.

### Web Services

Data-oriented web services are commonplace and useful, but they beg a question: Who is using which services?

1. Avoid web services calling other services calling others etc. as much as possible. Not only does this magnify overall latency, but it becomes difficult to trace dependencies.

2. Log web service calls, especially the "referer" HTTP header. If you must use service-to-service calls that have no referer, it's a good idea to send a header indicating "who" the caller is.

3. Block "external" access to services that aren't meant for external users, so that you don't have to account for the potential dependency (not to mention security concerns).

### Serialization AKA "Pickling"

Some programming languages - including Java & Python - have clever mechanisms for quickly dumping data structures into a stream of bytes and later reading them back in. The only catch is that this has *dependencies* on the programming language implementation and version, the data structure definition itself, various unknown quirks and contrivances, and so on, leading to potential disaster where deserialization suddenly fails and can't seem to fix itself.

The advent of XML and its eventual cousin (so to speak), JSON, gave us a more implementation-independent, even "human-readable" way to do the same thing, and thus these formats became quite popular. For XML, newfound relative ease gave people enough free time to obsess over DTD's, schemas, XSLT, and various excuses that resulted in a nightmarish bog of dependencies and data corruption distinctly reminiscient of early serialization tech, perhaps even worse, leading to a widespread XML revolt. Nowadays everybody hates XML, but most of them don't really know why - they just do.

The more recent generation of JSON enthusiasts have mostly resisted the temptations indulged in by XML enthusiasts of yore, but a chosen few doggedly and tirelessly lobby everyone else to yield to the old siren song of intricate schemas and meta-validations and what-have-you. My advice is, don't do it.

Of course we're not just writing things out and reading them back into the same application; we're shipping them over the network connection to parts unknown. When you are considering a change to some data structure, shouldn't you first check to see whether it's being serialized to and/or from remote systems? We're often using a fancy JSON library to do this serialization, so that all fields are automatically serialized and deserialized, often including things that *don't need to be*. This is really just to save a few lines of boilerplate programming.

Thus it makes sense to at least have some agreed-upon method of documenting, annotating, and/or roping off data structures so that other programmers know: Watch out - this is going over the wire! In fact there is room to argue that data structures should not be dual-purposed for both internal logic *and* as messaging structures. However, a lot of us will complain bitterly at this suggestion, that we have to create all these darned new structures and data-copying logic just to make that grumpy old dependency-obsessive so-and-so happy! Well, yes, the tradeoff is significant, but I still suggest you give this idea fair consideration.

### Asynchronous Communication

Message queues and hot-rodded derivatives like Kafka remain as popular as ever. The general idea of asynchronous communication and "reactive" programming is often sold as a magic potion rather than a tradeoff. There is a downside, in fact: It becomes hard to track down the *narrative* of a business process because there are no direct clues of "what comes after this." Data is dropped off in something queue-like, and - what? What picks up where we left off?

"What do you mean, I broke it? How was I supposed to know about that part?"

It would be silly to claim, however, that async communication should be forbidden; it is sometimes a necessity. Still, this type of system needs to be clearly justified in light of tradeoffs rather than adopting a knee-jerk "everything async" strategy. Documentation of your async architecture needs to be a priority so that software developers know where to look for the next link in the narrative chain and understand how they are accountable to it. Deserialization (as per above) comes into play here!

It might help to remember the mostly forgotten practice of using *database triggers*, where logic hidden in the database would change data unexpectedly without software developers being aware, because execution is implicit rather than explicit; while it might be hugely offensive to see one's hip & trendy new reactive programming technique associated with something so old-fashioned and fuddy-duddy, it is in fact the same thing (except it's not async), and we stopped using database triggers because they drove everyone crazy and weren't particularly necessary. Even now, when somebody comes to me with an Epic Database Mystery, my first question is, "Are you *sure* you don't have any database triggers?" Occasionally my first guess turns out to be right.

### Web Application Front-End

Front-end development on the world-wide web is often a dependency horror. We don't have any automation for checking dependencies first on CSS & JavaScript files themselves, and second dependencies within them - CSS selectors, JavaScript functions, etc.

When crafting and using file names for CSS/JavaScript, you should always be asking, "How hard will it be to figure out what is using this file?" Be careful about translation systems that do inconsistent and unpredictable mappings from URLs to files, making it that much more difficult to answer the question. The same holds for CSS selectors and JavaScript functions: What is using this? Being able to search for distinctive names is key.

A lot of people got overexcited when Cross-Origin Request Sharing (CORS) came out. CORS is a workaround for folks who have backed themselves into a corner, not an ideal solution. It means that you have confusing dependencies between different web sites/applications and have to make sure you do CORS correctly without creating security problems. In general your web site/application is going to be more robust when its dependencies come from the same domain. A lot of programmers still load resources from 3rd-party web sites instead of downloading and hosting them directly, which assumes those sites will always be available (they won't).

JavaScript is a pretty mature programming language nowadays, but it is still considered mandatory that one must adopt at least one "framework" like React, Angular(JS), etc. This creates a usually manageable dependency issue, assuming one's choice of frameworks doesn't go defunct. Unfortunately when that happens the only way out is to rewrite your entire front end. It becomes ultra-important to build high confidence that your chosen framework has a future, especially when you realize that security issues are going to force upgrades no matter what.

### People

Technology problems are hard, but people problems are often harder. So, there are the technical aspects of a complex change, and the people aspects: People don't always like your idea, and the more people, the less likely you are to establish consensus. Many organizations brag about their obsession with collaboration, but collaboration can get in the way of necessary change and bring about Death By Committee. Healthy organizations balance empowerment with collaboration, enabling people to act decisively when necessary.

There is a popular notion that microservices serve the purpose of managing the people dependency, by chopping applications up into pieces with each piece managed by a small, independent, empowered team. Some people insist this is the *only* excuse for using microservices, which is an error; you can use microservices for other dependency problems (more on that later).

### The Super-Monolith

When people say "monolith", they are often thinking of the *opposite* of microservices, an application where everything is wrapped up in one big ball of mud deployment. However, there's another animal to consider: The super-monolith.

Let's say you have an application team that's been working on Project X for a number of years. You decide they could use a change of pace, so you hand them: Project Y. The latter is totally unrelated to the former; all they have in common are abstract concepts like, say, "authentication", or "facilities". The stakeholders and even the users are different people. As you see it, Project Y should be an exciting opportunity to try new things and shake off the old "legacy" encumbrances. You imagine the team will be duly appreciative.

To your surprise, the team decides to combine Y with X! When you argue, the team points out that they've perfected many wheels in X, and they'd hate to reinvent them all from scratch. By combining, they can "hit the ground running". They point out that they can just use access control features to switcharoo the user interface between X and Y depending on who logs in. Managers will often step in and argue on behalf of the team's idea here - not against.

Are they right? No, they're horribly wrong, and there will be all sorts of bad consequences! The team is trying to build a super-monolith.

It's bad enough that the architecture of both X and Y will be twice as complicated now. Less obvious is that if X and Y are different lines of business in the organization, we've just made an executive call, to merge *the lines of business* regardless of what real executives think. Stakeholders will now have to battle for control with stakeholders they may have never met, which might be an opportunity to make new friends if it weren't for the battling part.

Be warned that if you fight Team Super-Monolith and refuse to let them merge X and Y, they may find clever ways around you, so that X and Y *look* superficially separate, but:

- All logic is moved into shared libraries that are sucked into both X & Y builds;
- They use CORS (Cross-Origin Request Sharing) to send HTTP requests all to the same back-end domain;
- The back-end systems send HTTP requests back and forth to each other;
- The back-end systems are all talking to the same database, message queue, memcached etc;
- Programmers copy and paste X, then hack on it until it looks like Y, and leave huge gobs of leftover X laying around in Y.

Often the effort to avoid reinventing a so-called "wheel" results in a much more complex wheel, because the old wheel isn't actually a universal wheel and doesn't account for new requirements. Especially with super-monoliths we often end up with a tug-of-war as software developers working with different stakeholders bend the wheel back and forth, not understanding the other person's bends. Most importantly, we are creating an additional web of dependencies where none should exist in the first place.

None of this is meant to denigrate the general idea of resource-sharing, i.e. shared libraries and frameworks, shared computing infrastructure, etc., or even just the sharing of *ideas*. The somewhat unconscious goal of the super-monolith is often to accomplish the same results using a "let's do this the easy way" mindset; establishing boundaries is hard, so let's just not have them!

Really, "monolithic" is a programming *culture*, not a technology. It's a mindset, a value system that says One Big Thing is always better than many small things. People think to themselves, "United we stand, divided we fall!" It's an *intuition*, and people tend to dig their heels in hard on their intuitions. At the very minimum it's important to remember that divide & conquer is an ancient, proven engineering strategy that should at least be balanced against our idea of unification.

Once a team goes all-in on its super-monolith, the decision is usually permanent, so that you're unlikely to win that fight retroactively. Worse yet, the super-monolith is a fairly reliable way to tank a business, and you'll need a highly talented team just to keep it afloat. At some point it's best to just move on to other things.

## Other Mitigations

*Architecture Review:* A lot of software development teams are focused solely on source logic review, where the last step in the development cycle is to someone else to review the work and recommend changes. Most teams don't have any process of architectural review where such changes are reviewed by more people and more carefully; many changes can simply fixed when they fail to work out, but architecture changes are extremely hard to reverse. Architecture review is an excellent place to capture dependency changes that could have significant long-term impacts.

*Architecture Documentation*: There's always That Guy who brags, "Properly written software is self-documenting." Well, that's at least partly true, but the big weakness in this thesis is the network, where we lose track of the narrative as things hop from node to node. Your biggest documentation bang for buck is in giving folks a high-level view of network-level interactions so that they understand what the network dependencies are; this can usually be done very quickly and concisely, enough to bootstrap someone into understanding the rest of what they need to know by, yes, reading that "self-documenting" programming logic. Most importantly of all, the moment of attempting to draw up the network-level design piecemealed into place over a couple of years is often where a dependency nightmare gains the light of day and reveals its ultimate horror. Expect people to try dodge the effort because of this.

*Microservices*: Again, the popular notion of microservices is as a solution for people dependencies, but there are other uses. For example, consider a payment processing system that is forced into severe lockdown because security personnel are reasonably paranoid about credit card data, but the team's "agility" is impacted by all the new bureaucratic security hurdles. Would it make sense to carve payment processing out into a separate runtime so that other work on our application can bypass the lockdown? Also consider the problem of a badly behaved but necessary driver, and how we might carve the surrounding logic out into a microservice so that it doesn't impact the rest of our architecture so badly. The result of these use cases is not microservices everywhere, but a larger "main" application with a couple of specialized microservices running alongside, which is manageable without completely diving down the rabbit hole of Endlessly Emergent Microservice Infrastructure Enhancements and hiring a devops team to manage all the EEMIE.

*Grep*: This might seem childishly simple, but one of the most important tools in your dependency management lifestyle is search - as in the classic Unix/Linux "grep" program - because so many of your dependencies are a matter of detective work. I tend to favor classical grep over fancy IDE stuff because pure text output facilitates additional text analysis, filtering and transformation. In fact I recommend learning to do this:

    find . -type f | xargs grep "mything"

This use of `find` and `xargs` yields a more flexible recursive grep than `grep -r`. At any rate, you need to become an expert grepper, so to speak, so figure out what works for you. It will be worth the trouble.

## Conclusion

My main goal here was to illuminate an important perspective on software, one that you haven't been taught to pay attention to. I am hoping that in the future you will consider this way of looking at things as part of your decision-making. Again, the problem of dependencies isn't easy or black & white, but you should be thinking about it as a core problem that affects everyone.
