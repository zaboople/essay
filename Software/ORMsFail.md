# Why ORM's Fail Us

ORM stands for Object Relational.... Management? Manager? Something like that. It's supposed to be about solving the "object-relational mismatch problem", which is this idea that relational databases are a bit "wrong", in that they don't respect our ideas about object-oriented programming. Once upon a time there were even lots of people working on the idea of an "object-oriented database", resulting in numerous failed and bygone products.

By this point most of us *should* recognize that RDBMS's (relational database management systems) aren't really "wrong", but it seems to be widely acknowledged that ORM's are "The Vietnam of Software Engineering", as [Ted Neward would say](https://www.odbms.org/wp-content/uploads/2013/11/031.01-Neward-The-Vietnam-of-Computer-Science-June-2006.pdf). Mr. Neward seems to put a bit too much blame on the perceived incompatibility of OOP vs RDBMS tech, however, when the real problem is bad tool design caused by overambition and anti-pragmatism.

*(Yes, the Vietnam War analogy is terribly overwrought, but let's move on)*

## Bang For Buck

The typical ORM feature-set follows a "law of diminishing returns" pattern, or simply put, a "bang-for-buck" curve like this:

          |                                                   o
          |                                o                                 o
          |                    o
          |            o
          |       o
    Bang  |    o
          |  o
          | o
          |o
          o___________________________________________________________________
                    Buck

This curve claims that when you're designing an abstraction, there are features which are both easy to implement, and easy to understand - thus your cost, or "Buck" is low; and yet they are useful enough - that's your "Bang" - to be worthwhile. So those items land on the left-hand side of our curve.

As we drift to the right, there are features which seem nice to have, and being idealistic, we might *insist* on having, but are hard to understand and harder to implement. These might give us an improved Bang, but are they worthwhile? Such features often become "leaky abstractions", which is to say, you can't use it well without understanding the intricate details of how it was written. There is also a higher risk that the implementor made mistakes that we as users (i.e. programmer-users) are left to discover and deal with as undocumented gotchas.

At the very farthest right, ORM features cause so much havoc and mayhem, they not only create a radical uptick in our Buck, but a *decline* in our Bang, and it doesn't get much worse than that.

## Why You Would Bother

It's commonly claimed that, "If you don't have an ORM, you're going to end up writing one." Not really: You most certainly can implement your object-oriented application without using any ORM at all. You're only going to write an ORM if you're ambitious and egotistical enough to think you *can*, and even then only inasmuch as you think it will be easy; in the beginning, it usually is, and if you can leave well enough alone, you'll do okay if you're competent. This approach is at the left of our BFB curve, where we'd be mostly focused on:

- Eliminating repetitive, tedious lines of boilerplate
- Preventing simple mistakes
- Making up for shortcomings in our original driver library

Arguably those goals fall short of any real "object-relational" ideology - more an ideology of convenience. Of course if you already have a suitable library at the ready, you can use that along with whatever customizations and minor gaps you might still want to fill in. Be warned, however, that popular ORM's typically go far to the *right*. I would like to explain how I think this happens.

## Some Classic Bad Design Principles

First we have an ideal of, "The Network Should Be Invisible". This is also the core philosophy behind RPC (Remote Procedure Calls). The idea is that networks are "hard", and ordinary programmers should not have to deal with them. This was a popular idea in the 1990's, and it's at least half right: Networks _are_ hard. However, pretending they don't exist just makes them _harder_, because it's so hard to control something that isn't supposed to be there.

Second, we have an ideal of, "It's Just Storage": A database is just a place you put stuff, like a closet, or a garage, not a newly redecorated living room that you show off to all your friends. A database seems like utilitarian dirty work, like something that should be uninteresting to a big-shot software architect like yourself.

These two collide into a third and most toxic ideal: *Data should appear to be in memory already*. This is one of the leakiest abstractions ever conceived, by design. It forcibly demotes I/O to the status of invisibility, which is a huge problem, especially when you are bottlenecked on I/O, which is usually the case.

Most of us don't mind when trivial things are implicit and handled invisibly, but none of this stuff is trivial. Making it implicit makes it hard to control and account for. If we have proper respect for our problem space, it should be easy to understand that we need explicit control over it.

To top all that off, we have a problem of, "The programming language is broken, so let's fix it!" When things like lambdas, var-args, and multi-line strings are unavailable, it's too easy to justify horrible workarounds; at the very least, don't blame that on your RDBMS. Fortunately the Java elephant in the room has mostly come around, and its practitioners should do some rethinking.

## The Feature-Set of Badness

### 1. Query Generation

Definitely writing SQL "by hand" results in a lot of boilerplate. It's also easy to make mistakes, what with all the commas and parentheses. Your compiler cannot catch those mistakes when they are sitting inside dynamically written strings, so at a minimum you have to test carefully and thoroughly - and no, classical unit tests aren't gonna cut it.

At this writing a lot of languages _still_ don't have multi-line strings, so in those cases you end up with lots of `+` operators mashing things together. They don't have String interpolation either, so you get a few more `+` operators. These shortcomings make a generator all the more appealing.

SQL is not a simple language, but the most common queries are trivial. You could probably write a basic SQL generator in a day, finish testing it in another day, and cover 50+ % of your use cases. That's on the left hand of our BFB curve.

That last 25% is much harder. The challenge is making the generator API less repetitive and awkward than the SQL itself; a lot of them do the opposite. Also tricky is ensuring the generator is as easy to understand as SQL. Struggling to learn a library that is harder than SQL itself is silly. Worst of all is a library that generates incredibly horrible queries - watch out for the ones that try to make SQL "functional" (we didn't even talk about functional-relational-mismatches - ugh).

So we've already found our first way to spiral out of control: Write a feature-complete SQL generator instead of a minimalist one, especially a SQL generator that tempts you to believe there's no need for you to know anything about SQL at all.

### 2. Marshalling

"Marshalling" is the dirty work of moving bits and pieces of data back and forth across the SQL/driver boundary. Things like:

    foo.bar = resultSet.getInt("bar") // data coming in

and

    statement.setInt(1, foo.bar) //data going out

That logic doesn't look so bad as one-liners, but those lines multiply rapidly, and there's lots of opportunities for misspellings of SQL column names and mismatched column indexes. There's also the problem of data type conversion, where we want to do little tweaks to numeric types, dates, etc. Some of this could be addressed with a low-key, ORM-ish library, but none of it is drop-dead, showstopper devastating - just inconvenient.

Enter metaprogramming: By adding some meta-data that tells us which tables and columns map to which fields in a programming data structure and how, we can reduce our overhead a little bit, down to something like:

    Foo foo=orm.getRecord(new Foo(), id);
    ...
    orm.update(foo);
    ...
    orm.insert(new Foo(...));

The above also requires us to meta-describe primary key information, accounting for auto-generated keys when we use them. This can get complex if we demand that the ORM author start handling special cases like objects that are spread across more than one table, custom data converters, and so on, but if they are reasonably conservative and resist the temptation to indulge every entreaty, they can stay mostly on the "good" side of the bang-for-buck curve. Unfortunately most ORM's do the opposite, so that as a user you'll have to deal with excessive complexity even when you don't need it.

### 3. Implicit Writes and Connections

Suppose we have a one-to-many relationship between A & B, which might lead to class definitions like:

    class A {
        public List<B> getBs();
    }

    class B {
        public A getA();
    }

This creates an interesting opportunity for an ORM designer. Shouldn't this "easiest-to-use" ORM automatically handle the fetching of related objects? The API user should only need to provide some metadata about foreign keys, and the ORM can infer the necessary SQL from there.

That metadata is going to need to include something about lazy-loading: Given how foreign-key relationships propagate across a database, we could accidentally end up loading thousands or even millions of records if the ORM "eagerly" fills everything in. So we'll usually put some extra metadata in, something that looks like `lazyLoad=true`.

How the heck is an ORM designer going to implement this magic? One way is to create a custom List implementation that holds an internal reference to a database connection and knows to start loading when `List.get()`, `List.size()` etc. are called. Of course if you call `getBs()` after the connection is closed, this will blow up on you even though it looks a normal method call on A. The abstraction leaks. Even `B.getA()` is suspect, as the ORM may try to "monkey-patch" in a lazy-loading trick move at runtime.

In the end, this starts becoming so confusing and complicated it isn't worthwhile; it's too far out on the bang/buck curve.

### 4. The Cache

Suppose my function calls yours calls his calls hers:

    mine()->yours()->his()->hers()

Each function reads and writes various data. The different authors are largely unaware of what the other authors have done. What are the odds that someone's function accidentally overwrites database changes that another function is trying to write? This can happen even though the calls are synchronous, i.e. there is only one thread of execution passing through these functions.

One popular way to handle this is with a "smart" cache inside the ORM that maintains a lookup table of every unique database record. If two functions reach for the same record, they both get a reference to the same in-memory structure, not two different copies, so that neither loses the other's changes. Of course anyone who's ever implemented caching learns: It's never as easy as it looks. The cache can rapidly grow huge, and it can introduce even more problems with staleness and lost updates. Many ORM's turn on this caching by default, so that programmers don't even know it's happening, and by that point, they'll realize that turning it off could potentially break things that implicitly rely on it.

Here's a different take on the problem: Why are these programmers ignoring each other's work? Why do we have so many different cooks in the same kitchen? Most of all, why are we spreading the logic of our business process over so many function calls?

This is forced by the popular idea of the "Rich Domain Model", which insists that the "entity" objects - the in-memory objects copied to & from the database - should implement all of the "business logic". Rather than creating one function to run a given business process, we have to chop the process up and distribute the parts across all the participants - and thus, endless method-hopping back & forth. Our I/O-bound local-remote duality is brushed aside as an implementation detail that our powerful abstractions can surely manage out of the way. The chopping-up makes it impossible to write a single coherent narrative that any contributor *sees* and understands well.

RDM is also going to lead to implicit writes, because our highly distributed logic might be making multiple changes to the same object, so it makes no sense to write it each time, but it's also difficult to figure out where the last update is. Now the ORM is also trying to figure out whether a record changed by doing more runtime trickery.

Eliminate the Rich Domain Model, and our caching problem dissipates. Of course this can be difficult to do on teams where RDM is quasi-religious dogma disguised as "engineering", and holy war is no fun for anybody. The best you can do in some cases is to advocate for the importance of a coherent narrative and try to change people's minds, futile as that may be. Feel free to use the old "There two hard problems in computing" joke: Naming things, caching, and off-by-one. (We really need some new jokes...)

(A somewhat sillier take on RDM [here](./TheRichDomainModel.md))

### 5. Connections (again) & Transactions

Consider the following based roughly on Java JDBC:

    var conn = getDBConnection();
    try {
        conn.setAutoCommit(false);
        processWith(conn); // Do actual things
        conn.commit();
    } catch (Exception e) {
        log(e);
        conn.rollback();
    } finally {
        conn.close();
    }

That is a lot of boilerplate! Nowadays, however, this is (mostly) trivial to abstract away into a lambda. Historically, people would try to invent all sorts of aspect-oriented trickery with annotations and meta-stuff that seemed really clever, but yet again went to bang-buck heck. Do it the easy way, not the hard way.

## Conclusions

What gets us in trouble:

- Too much meta-programming, and thus too much complexity
- Too much implicit behavior where explicit behavior is completely sufficient and usually necessary, esp. with I/O
- OOP elitism justifying a desire to make programming more complicated than necessary

It *is* possible to build enhancements for a library like JDBC where repetitive programming could be eliminated, if we act conservatively. The key is to recognize that once you have a database driver, you'll be able to get the job done no matter what, and that predictable, straightforward behavior is more important than exotic features or trying to get everything written in the fewest possible lines.

One might also ask a question, as to which is worse:

- A) You messed up your object-oriented design in your application logic, and need to fix it;
- B) You messed up your relational database design.

Anyone with real experience knows that B) is far worse, because it involves brutal, risky, touch-and-go data migration where A) only requires a rewrite and redeploy. Many programmers believe the right way to design is objects-first, but I recommend going database-first. The wrong storage plan is going to have much worse consequences, and storage tends to outlive programming languages.

Of course some reputable folks (such as [Jeff Atwood](https://blog.codinghorror.com/object-relational-mapping-is-the-vietnam-of-computer-science)) occasionally insist that the only option is to abandon RDBMS tech entirely, or abandon OOP entirely; this is grumpy baby-bathwatering. You may need to adopt a less dogmatic, cult-like approach to OOP, but that's a good idea even without RDBMS in the picture.

----

[Back to Software main page](./README.md)
