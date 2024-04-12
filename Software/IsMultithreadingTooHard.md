# Is Multithreading Too Hard?

There seems to be a popular assertion that multithreaded logic - effectively, concurrency - is really, really hard, so hard that you just shouldn't even try it. A lot of this comes down to shared memory juggling between threads. Most languages have a "synchronized" keyword that you can use to create exclusive locks on data so that multithreaded access is safe, if not completely straightforward.

When I'm fixing someone's multithreading mistakes, it's usually not because threads are "too hard", but because that someone refused to follow the rules of thread synchronization, doing silly things under the delusion that they've achieved high-performance, lock-free supremacy. Thread synchronization does hurt performance, but thread/data corruption is not an improvement. There are documented techniques and libraries for achieving the lock-free grail in some circumstances, and if it's really needed, that's the best way to go; most of the time the performance issues are not real or locks are not the actual bottleneck hurting us. To fix a performance problem, you must first find the *source* of that problem.

I'm definitely not convinced that you should switch programming languages to chase some sort of "multithreading made easy" paradigm; the complexity and gotchas always show back up, be it with actors, coroutines, or some other such thing. It's fun to learn a tricky new thing when I have no choice but to do someone's bidding, but they're just paying me to re-solve a problem - and pay me they have...

It's worth pointing out that in any system using relational database transactions, you have the exact same problem as synchronized thread locking: Database read/write locks. The problem of deadlocks in an RDBMS is exactly the same as it is in multithreading with lock synchronization. Actually, unlike RDBMS transactions, it is often possible to avoid locking more than one thing at a time in ordinary multithreading, and deadlocks can't happen if you have only one thing locked.

I should admit that *concurrency* is hard, no matter what concurrency paradigm I'm using. Even non-blocking I/O - which is sort of "anti-multithreading" - is hard when I have to use special concurrency tricks to get it, whether it's Actors, Futures, Promises, Futures that are called Promises, etc. The exception is Google Go - and soon-ish, Java - where non-blocking was made completely implicit and practically invisible, so that I can't mess it up. For now, I've gotten the hang of all the variations, even in JavaScript.

Programming is hard.

----

[Back to Software main page](./README.md)
