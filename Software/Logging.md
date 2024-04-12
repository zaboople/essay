# Logging, Properly

Logging is essential and valuable, but often ruined through abuse. Below are some general principles I follow, not as absolute mandates, but fairly good guidelines for a typical application development environment.

## Communicate effectively

You write to be read, and if you want to be read, write log messages that get the reader's attention with good punctuation, capitalization, grammar, and so on, because you *respect* your reader. This is what professional writers do, and as a programmer you are also a writer.

In production systems, log messages should not be _cryptic_. This means they should not require intimate familiarity with the application's internal logic. Someone who understands the general technical & business layout of the land - say, a systems administrator, or even a manager - should be able to look at the log and get an approximate idea of "This is what is happening right now". They should be able to understand whether the software is humming along normally or gravely upset about something.

## Have lots of log space

Often I find that it's impossible to gather adequate information because we've shortchanged the logging storage system with inadequate disk space. This is rather tragic given that disk storage is usually the cheapest hardware problem.

## Text is Powerful

I write software using text because text is language, and language has power. I log text messages because still that text has power. I can use zgrep and maybe a dash of perl/gawk/python/etc to surf logs and gather data, provided it has a decently predictable structure - no it doesn't actually have to be formal JSON or XML.

## Info & Error

The two most important kinds of logging are "info" and "error". With these two you can do just about everything you need to do, if you use them properly; if you _abuse_ them, you end up with more problems, and if you try to compensate for that abuse instead of solving the original problem, you will end up with even more problems again. The only difference between info & error is that we put a consistent signifier in our log lines, like "ERROR" or "INFO".

We should designate "INFO" as a default signifier, to say, "This is just what's happening now, FYI." No alarm bells need go off.

We should designate "ERROR" to mean: This is broken, so fix this ASAP. Either the error message itself is an error - we shouldn't have logged one, but we did by accident - or there really is an underlying problem; either way, it means we have work to do. This allows us to proactively discover problems by just searching logs for "ERROR". We can even set up automated scanning and messaging to let us know.

All of this sounds simple at first, but our usage of "ERROR" is only useful if we are disciplined about it. Things fall apart when someone says, "Well, that ERROR is an error, yes, but, well... I don't have time right now, and hey, if it was really a _problem_, somebody would call us and complain, or something, so we'll just have to let it go for now..." As this happens over and over, "ERROR" loses any meaning, and we have no way to say, "Something is definitely wrong." We've effectively retold the fable of The Boy Who Cried "ERROR". Now we can't be proactive about problems; we have to wait until they escalate into full-blown fire-breathing emergencies, wait for someone to call, and then sift through the ERROR's until we find out what is so much worse than usual - of course it may be that the problem wasn't logged at all.

The real problem here is semantics: There are actionable errors, and unactionable errors. For example, suppose a third party service integration is temporarily offline and there's nothing we can do about it. This will cause an error like the infamous "502 Gateway Timeout". Shouldn't we log it? Yes, but not flagged as "ERROR" if we can't fix it, and the 3rd party SLA might tell us that we agreed to accept 0.1% downtime anyhow.

It's also common for people to assert a perfectionist-procastination mentality of "This should be fixed  ...eventually." If we can let it slide for months going on years, the imperfection was either unimportant or our business goals have become unmanageable; either way, it's *non-actionable*. The point of our ERROR designation was to let us know about problems we can control and intend to fix, not to wring our hands and shrug at the same ERROR over and over without doing anything.

Some people use the signifier "WARN" to prefix non-actionable errors, which is reasonable but not absolutely necessary. At that point we only have three signifiers, which is still manageable and straightforward.

As a new system goes into real-world production, I often find it necessary to refine the error handling and logging as non-actionable errors reveal themselves and I deal with the dissapointing and unfair fact that many things in life are beyond my control. Sometimes I can insert a workaround, such as implementing retry logic for the aforementioned 502 problem, assuming it is worth the trouble; I don't want to fall into that trap of perfectionist procrastination.

## Debug logging

There is also a popular idea of "debug logging", which is basically "stuff you won't understand unless you have intimate knowledge of the source logic". Then we have to solve the problem of disabling this in production, else we impact performance and create a cognitive overload for analysis by generating huge volumes of log data. If you have a strategy for dealing with this (i.e. fancy logging frameworks), great, but in many cases it will never be useful beyond the initial development cycle. I often delete debug-logging leftovers when doing maintenance overhauls because they only made sense to one person and get me nothing. I'm not losing functionality, just gibberish.

There are *some* command-line tools in Linux and other operating systems that offer a "--debug" option to get additional information printed as the software does its work, for diagnosis and troubleshooting - `openssh` comes to mind here. This is helpful, but note that most tools don't offer this feature; openssh is a nasty bugbear, arguably by necessity, and so the feature is useful. My goal is to first avoid writing a nasty bugbear if I can.

Some logging libraries add not only a DEBUG signifier, but a TRACE as well, and maybe a few others. I can't even tell you the difference, and am hard-pressed to care.

## Fancy online log databases

A lot of people talk about the "ELK stack", which involves a very fancy method of collecting log data and shipping it off to a database with a web front end that makes it "easy" to query large volumes of data. This is yet another idea that sounds great in principle, yet: When I ask, "How do I get to logging information?" people are happy to tell me _where_ to go, but then it turns out nobody knows how to use it. Only one or two people can figure out the user interface and its highly obscure query engine. Worse yet, it turns out the database is collapsing on a regular basis and people are struggling to keep it running consistently, which means one of our software management systems has its own management problems (should it log the errors into itself?)

Software engineering often involves a progression from simple solutions that work well at small scale to elaborate solutions that work better at large scale. We have to make cost judgements about over-simplifying vs. over-elaborating and how well something fits our organization. A lot of the problems I've seen are likely caused by small organizations trying to act like gigantic organizations that can afford the expense of managing complex meta-software. So it's not as if ELK is always wrong, just wrong for a lot of smaller operations.

----

[Back to Software main page](./README.md)
