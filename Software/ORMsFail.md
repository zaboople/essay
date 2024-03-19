# Why ORM's Are't Practical

ORM stands for Object Relational.... Management? Manager? Something like that. It's supposed to be about solving the "object-relational mismatch", which is this idea that relational databases are a bit "wrong", in that they don't respect our ideas about "objects". Once upon a time there were even lots of people working on the idea of an "object-oriented database", resulting in numerous failed and bygone products.

By this point most of us *should* recognize that RDBMS's aren't really "wrong", but it seems to be widely acknowledged that ORM's are "The Vietnam of Software Engineering", as Martin Fowler would say, yet he can't explain why. Herein we attempt to explain.

## Bang and Buck

The typical ORM feature-set follows a "standard" bang-for-buck curve, which looks like this:

          |                                               o
          |
          |                      o                                                        ?
          |
          |          o
          |
    Bang  |    o
          |
          | o
          |o
          o________________________________________________________________________________
                    Buck

This curve claims that when you're designing an abstraction, there are features which are both easy to implement, and easy to understand - thus your cost, or "Buck" is low; and yet they are useful enough - that's your "Bang" - to be worthwhile. So those items land on the left-hand area of our curve.

As we drift to the right, there are features which seem nice to have, and which we might think we _must_ have, but are hard to understand and harder to implement. They might give us an improved Bang, but we are compelled to ask whether it is worthwhile. These kinds of features often end up being "leaky abstractions", which is to say, you can't use it well without understanding the intricate details of how it was written. There is also a higher risk that the implementor made mistakes that we as users are left to discover and deal with.

I put a question mark on the far right because at some point our features cause so much havoc and mayhem, they not only create a radical uptick in our Buck, but maybe an actual decline in our Bang - and that's how we got this "Vietnam" thing.

## Why You Would Bother

A common claim is that "if you don't have an ORM, you're going to end up writing one". Well, no, you're only going to write one if you're ambitious and egotistical enough to think you *can*, and even then only inasmuch as you think it will be easy; in the beginning, it usually is.

At the left of our BFB curve, we'd be mostly focused on:

- Eliminating repetitive, tedious lines of boilerplate
- Preventing simple mistakes
- Making up for shortcomings in our original driver library

The right-hand side of our BFB curve involves quite a bit of explanation.

## Some Classic Bad Design Principles

First we have a design principle of, "The Network Should Be Invisible". This is also the core philosophy behind RPC - "Remote Procedure Calls". The idea is that networks are "hard", and ordinary programmers should not have to deal with them. This was a popular idea in the 1990's, and it's at least half right: Networks _are_ hard. However, pretending they don't exist just makes them _harder_, because it's so hard to control something that isn't supposed to be there.

Second, we have a design principle of, "It's Just Storage": A database is just a place you put stuff, like a closet, or a garage, not a newly redecorated living room or dining room that you show off to all your friends. A database seems like utilitarian dirty work, like something that should be uninteresting to a big-shot software architect like yourself.

These two principles collide into a third, the most toxic possible idea: *Data should appear to be in memory already*. This is one of the leakiest abstractions ever conceived, by design. It literally demotes I/O to the status of invisibility, which is a huge problem, especially when you are bottlenecked on I/O, which is usually the case.

Most of us don't mind when trivial things are implicit and handled invisibly, but none of this stuff is trivial. Making it implicit makes it hard to control and account for. If we have proper respect for our problem space, it should be easy to understand that we need explicit control over it. This is where much of our trouble starts.

## The Feature-Set of Badness

### 1. Query Generation

Definitely writing SQL "by hand" results in a lot of boilerplate. It's also easy to make mistakes, what with all the commas and parentheses. Your compiler cannot catch those mistakes when they are sitting inside dynamically written strings.

At this writing Java & JavaScript _still_ don't have multi-line strings, so in those cases you end up with lots of `+` operators mashing things together. They don't have String interpolation either, so you get even more `+` operators. These shortcomings make a generator all the more appealing.

SQL is not a simple language, but the most common queries are trivial. You could probably write a basic SQL generator in a day, finish testing it in another day, and cover 75% of your use cases. That's the left hand of your BFB.

That last 25% is much harder. The really tricky thing is making the generator API less repetitive than the SQL itself; a lot of generators do the opposite. Also tricky is ensuring the generator is as easy to understand as SQL. Struggling to learn a library that generates SQL that you're also struggling to understand is... silly. Worst of all is a library that generates incredibly horrible queries - watch out for the ones that try to make SQL "functional" (we didn't even talk about functional-relational-mismatches - ugh).

So we've already found our first way to spiral out of control: Write a feature-complete SQL generator instead of a minimalist one, especially a SQL generator that tempts you to believe there's no need for you to know anything about SQL at all.

### 2. Marshalling

"Marshalling" is the dirty work of moving bits and pieces of data back and forth across the SQL/driver boundary. Things like:

    foo.bar = resultSet.getInt("bar") // data coming in

and

    statement.setInt(1, foo.bar) //data going out

That logic doesn't look so bad as one-liners, but those lines multiply rapidly, and there's lots of opportunities for misspellings of SQL column names and mismatched column indexes. There's also the problem of data type conversion, where we want to do little tweaks to numeric types, dates, etc.

Enter metaprogramming: By adding some meta-data that tells us which tables and columns map to which fields in a programming data structure and how, we can reduce our overhead a little bit, down to something like:

    Foo foo=orm.getRecord(new Foo(), id);
    ...
    orm.update(foo);
    ...
    orm.insert(new Foo(...));

The above also requires us to meta-describe primary key information, accounting for auto-generated keys when we use them. This can get complex if we demand that the ORM author start handling special cases like objects that are spread across more than one table, custom data converters, and so on, but if they are reasonably conservative and resist the temptation to indulge every entreaty, they can stay mostly on the "good" side of the bang-for-buck curve. Don't forget: That curve applies to the ORM user as much as the author.

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

Think about it: How the heck is an ORM designer going to implement this magic? One way is to create a custom List implementation that holds an internal reference to an ORM session and knows to start loading when `List.get()`, `List.size()` etc. are called. Ooh, fancy that. Of course if you call `getBs()` after the ORM session is closed, it will blow up on you even though it looks a normal method call on A.

In the end, this starts becoming so confusing and complicated it isn't worthwhile; it's too far out on the bang/buck curve.

### 4. The Cache

Here is a interesting problem: My function calls yours calls his calls hers:

    mine()->yours()->his()->hers()

Each function reads and writes various data. The different authors are largely unaware of what the other authors have done. What are the odds that someone's function accidentally overwrites database changes that another function is trying to write? This can happen even though the calls are synchronous, i.e. there is only one thread of execution passing through these functions.

One popular way to handle this is with a "smart" cache inside the ORM that maintains a lookup table of every unique database record. If two functions reach for the same record, they both get a reference to the same in-memory structure, not two different copies, so that neither loses the other's changes. Of course anyone who's ever implemented caching more than a couple of times learns: It's never as easy as it looks. The cache can rapidly grow huge, and it can introduce even more problems with staleness and lost updates. Many ORM's turn on this caching by default, so that programmers don't even know it's happening, and by that point, they'll realize that turning it off could potentially break things that implicitly rely on it.

So, here's a different take on the problem: Why are these programmers ignoring each other's work? Why do we have so many different cooks in the same kitchen? Why are we spreading the logic of our business process over so many function calls?

This is forced by the popular idea of the "Rich Domain Model", which insists that the "entity" objects - the in-memory objects copied to & from the database - should implement all of the "business logic". Rather than creating one function to run a given business process, we have to chop the process up and distribute the parts across all the participants - and thus, endless method-hopping back & forth. The fact that we are operating in an I/O-bound duality is brushed aside as an implementation detail that our powerful abstractions can surely manage out of the way. The chopping up makes it much harder to write a single coherent narrative that the author understands and *sees* clearly.

RDM is also going to lead to implicit writes, because our highly distributed logic might be making multiple changes to the same object, so it makes no sense to write it each time but it's also difficult to figure out where the last update is.

Eliminate the Rich Domain Model, and our caching problem dissipates.

## Conclusions

What gets us in trouble:

- Too much meta-programming, and thus too much of a learning curve to go with
- Too much implicit behavior where explicit behavior would be sufficient, esp. with I/O
- OOP elitism driving a desire to make programming more complicated than necessary

It *is* possible to build enhancements for a library like JDBC where repetitive programming could be eliminated, if we act conservatively. The key is to recognize that once you have a database driver, you'll be able to get the job done no matter what, and that predictable, straightforward behavior is more important than exotic features or trying to get everything written in the fewest possible lines.
