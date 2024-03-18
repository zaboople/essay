# Which Environment Am I In?

A long time ago I had to deal with a database administrator who was deleting old databases on a development/testing server, only to realize it wasn't the server they thought; it was actually the production server! They were able to restore from backup and lose "only" a day's work for all of the users.

This is actually a common problem: "I thought I was in the test environment, not production!"

Also that day I did the job of hearing out complaints from angry users, and one of them actually had good advice: Set up color-coded environments for remote logins on servers, as Green, Yellow or Red, based on how critical they are - red being those mission-critical production systems. On Unix/Linux Bash this is very easy to do with your shell prompt using the PS1 environment variable. With Windows Remote Desktop you can simply change the desktop background color with a couple mouse-clicks.

Really, any kind of remote administration tool should have color-coding or other techniques for clarifying the environment you're in. Lately I've been using a graphical tool for examining data on my production database server, but I use it with a read-only account. When I need to do actual database-administration functions (including just updates & inserts) I use the standard command-line tool. This has prevented me from me making a mistake on several occasions. It's silly that I have to use different _applications_ in order to keep my brain straight, but neither tool has any concept of user-profiling that communicates the idea of "Remember: You're in production using an admin account right now!"

I've also done this directly in custom web applications, so that test users - who are sometimes end users - see directly in their browser: "This is a test instance". Sometimes it's already obvious because our test system is full of joke/junk data that you'll never see in production, but especially when we test with a copy of production data it can be hard to tell.

----

[Back to Software main page](./README.md)
