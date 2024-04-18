# Dependency Injection Part 2: A Good Idea Becomes a Huge Mess

This stands alone pretty well, but refer to [Part 1](./DependencyInjectionPart1.md) if you are the completist type or you still haven't figured out what dependency injection is supposed to mean.

## A Very Subtle Form of OOP Abuse

Consider two web application libraries. Each is very similar to the other, in that you create a web server instance, and then hand it some logic that you've written, logic that will handle HTTP requests that the library pre-processes for you. So maybe there's an interface like:

    public interface Handler {
        public void handle(HttpStuff stuff);
    }

Then you'd make a bunch of classes named MyHandler, MyOtherHandler and MyOtherOtherHandler, and pass them in to your framework for processing. Suppose Library 1 works like this:

    new Server1(...)
        .add("/", new MyHandler(a, b, c))
        .add("/foo",  new MyOtherHandler(a, b))
        .add("/bar",  new MyOtherOtherHandler(b, c))
        .startHttpServer();

There's nothing special about Library 1. We create instances of things that handle HTTP requests first, doing some dependency injection of these a & b & c things, whatever those are. Then we start up our web server and everything is ready to go. Nothing to see here; dependency injection is as obvious as the proverbial noses on our faces. Library 2, however, is _slightly_ different:

    new Server2(...)
        .add("/", MyHandler.class)
        .add("/foo",  MyOtherHandler.class)
        .add("/bar",  new MyOtherOtherHandler.class)
        .startHttpServer();

The difference is that we don't give Library 2 _objects_ - we give it classes (or class names), and it does "reflection" magic to create instances of our objects. Then our objects are dropped helplessly into the HTTP jungle. This means we can't do dependency injection beforehand. Our "Handler" instances have no way to share information and resources. The only solution is to use global variables for a, b & c.

Library 1 makes dependency injection a no-brainer, and we don't need a special framework for dependency injection because we are doing object-oriented programming properly instead of sabotaging it from the get-go. The failure of Library 2 is not OOP's fault, but our lack of respect for it, and so Library 2 will soon attempt to compensate with a DI metaprogramming "framework". We'll have to use some sort of metadata system to drop hints for Library 2 to recognize that it needs to first create instances of a, b & c and then put them into our constructor methods. Of course it needs special metadata in order to create a, b & c as well, and soon we have a recursive metadata morass.

## The Dreaded Java Enterprise Edition

Guess what? Library 2 - *Framework* 2, really - is basically Java Enterprise Edition. In JEE, an application is made of Servlets, and each Servlet is provided as a class name to the framework, and instantiated (created) for you without any chance to intervene and get in front of the speeding train. There is no single point of entry. To some people this sounds very object-oriented, but again, the result is _anti_-object-oriented.

JEE did include a dependency injection workaround framework called "JNDI", but it was confusing and very limited; and it often simply didn't work, for reasons that couldn't be explained. Some open source folks decided to come up with a "better" way, and that was named, "Spring". Now the trouble really begins.

[foo](./bar)

The goal of Spring was _not_ to eliminate "static singletons" however - basically global variables, as mentioned in [Part 1](./DependencyInjectionPart1.md#global-functions) - because there is no other way to share these dependencies. Neither was the goal to do a proper redesign of JEE and eliminate the problem at its source. Rather, the goal was to hide, disguise and obfuscate static singletons until we can *pretend they aren't there*. This is done with all sorts of reflection tricks, which is to say, Meta-programming: reflection, xml, annotations, yaml, json, whatever.

The original design worked like this: Instead of putting your dependency injections into your Java programming logic, they go in a file named "application.xml". Spring reads all this in as metadata, using reflection to "instantiate" all the necessary objects, and stuffs them into a static (global) `Map` object where they can be looked up and cross referenced for use in other objects as things roll along. It's convoluted, but it's basically rewriting Java as XML. Something like this:

    <bean id="MyThing" class="Mine"/>
    <bean id="YourThing" class="Yours">
        <property name="mine" value="MyThing"/>
    </bean>

Of course our compiler can no longer validate the arrangement, so we wait until runtime to find out if we meta-programmed the wrong way, which we probably did. It's a lot like working in a scripting language, which kind of defeats the purpose of using a compiler. Using XML as a programming language is awkward, but at least everything is in one place, just like with main(). You always know where to look. Then two things happened:

1 Everybody got mad at life in general - war, pestilence, famine, the usual - and agreed to [blame XML](https://en.wikipedia.org/wiki/Scapegoating#Scapegoat_mechanism).

2 Java was blessed/cursed with a new feature: @Annotations.

So, Spring was revised to redo all that XML as Java annotations. Wait... how could that work? Well, instead of being all in one place, our dependency injection is scattered to the four winds as a sort of publish & subscribe system. So an annotation in class A says, "@I am publishing myself as an IThing" and an annotation in class B says, "@Boy, I really could use an IThing right now." Spring scans the entire application at runtime and juggles everyone around until all the subscribers are hooked up to the things published by the publishers. This is time-consuming to compute, and it's also hard for humans to reverse-engineer. Eventually people added the ability to limit the scanning only to designated libraries because it slows things down so much, so other people added the ability to override that scanning and force it anyhow, so now it's a tug-of-war. While writing Java in XML *is* bad, the annotation solution is arguably even worse.

At any rate, the aforementioned static singleton/global Map is hiding out as something called the "application context", camel-cased as `ApplicationContext`. You can query it if you like, but other programmers might shake their fingers at you and remind you that it's your job to pretend it isn't there.

Spring is horrible, but it was created to compensate for JEE, which was arguably horrible-er, and Spring became popular because JEE made it seem like a good idea. It's worth noting that JEE was so bad, and Spring so good by comparison, that JEE evolved to be more like Spring, and other frameworks evolved as well, all trying to be like Spring, but maybe "one better".

Believe it or not, many (if not most) Java programmers think this whole arrangement is quite brilliant. In fact Spring is considered Standard Dogma and The Right Way. If you come from C, Python, Lisp, or just about anything else, you may say, "This is nuts." In fact, Spring is usually where new-to-Java folks throw up their hands and declare "I hate Java!" even though the point of contention isn't Java (or even OOP). The Java programmers are usually inclined to assume this is because other people are Bad and don't understand the subtleties and importance of dependency injection; of course if you are trying to learn DI by using Spring, you might never understand it.

## Some Boring Historical Perspective

Why not just fix the problem that started all this, in Java Enterprise Edition itself? After all, not even Java programmers think JEE was a good idea. In fact, now that microservices are all the rage, everybody is using the new Spring microservice architecture, "Spring Boot", and behold, your get your main() back! Except... yet again they preferred classes over objects, making dependency injection impossible without metaprogramming. Why? Because otherwise Spring wouldn't make sense, although I'll admit it has other features that make it somewhat useful, if you can put up with the bad parts.

You _do_ have other options, of course: Jetty, Tomcat, Undertow and a few other web server libraries allow you to make your web services & applications & things object-friendly and thus DI-friendly. You can write main() the way main() was meant to be written.

## Bonus: ThreadLocals

Some years ago Java came up with this new thing, ThreadLocals. The idea is that you have a sort of generic wrapper that you can put around any arbitrary type - a string, a number, a list, whatever - and associate with it the "current" thread. So if you have web server handling multiple requests at the same time, each request can store data in the same ThreadLocal without any conflicts. This requires a little bit of advanced understanding, though - just to get started, you have to understand multithreading, and lot of programmers really don't.

So, if you use ThreadLocals because your local Chief Architect vouched for them and you've kind of shrugged it off and said, well, okay - it might come as a surprise to discover exactly what ThreadLocal is for: Global variables. Yes, another problem with global variables is that they don't work out so great in a multithreaded environment, but ThreadLocal fixes that! It's just that ThreadLocal doesn't fix any of the other problems.

Let me put it this way: If you're feeling smug and self-satisfied about your expert knowledge of design patterns like Dependency Injection, looking down your nose at lesser programmers who "just don't get it", and yet you use ThreadLocals, you are a complete hypocrite. If you never had any design pattern pretentiousness to begin with, and you flaunt your global variables with a devil-may-care flare, well, I guess at least you aren't a hypocrite.

Strangely, Spring encourages ThreadLocals. It also has numerous ThreadLocals hiding out in its own source logic. This is utter hypocrisy, but it continues Spring's pattern of doing the specific thing you tell everyone not to do.

### Side Note: The Request UUID Workaround

Probably the best excuse for ThreadLocals I've ever seen is this: His web service calls her web service calls your web service calls my web service. Suppose something is wrong for a given user at a given time and we're trying to trace down what happened; as we cross reference our way through the individual service logs, we realize the only way to match things up is to look at timestamps, hoping that the servers' time settings are reasonably well-synchronized (don't bet on it).

How about this: At the beginning of this chain of http requests we create a UUID, and pass it from server to server, and whenever we log something, we include the current UUID. Now, we just grep all the things for that UUID and voila: The sequence of events pops right out. The catch is that this is extremely tedious to put in place without ThreadLocal, esp. when you consider 3rd party logging. We need a ThreadLocal-aware logging system - these exist, fortunately.

Of course there's another way to look at it: This is service spaghetti. How and the heck did things get stretched across all these different servers in the first place? Are these services truly "decoupled" into free-standing, independent components, or have we simply run a monolithic business process through the slap-chopper such that four parts came out the chute at the other end? Perhaps I am being overly idealistic. Perhaps slap-chopping everything every which way isn't a good idea. Anyhow, that's the best reason I can come up with.

----

[Back to Software main page](./README.md)
