# The Server Is Down! (for junior programmers)

There's a lot that can be said about networking, but let me illustrate my pet peeve:

- "I can't get to the server! Can anyone get to the server? I think the server's down!"
- "Can you ping it?"
- "Yes I tried pinging it and I can't! So it must be down!"

From here people run in circles for an hour or two because we aren't answering two essential questions:

1. Is DNS resolving the server's name to the correct IP address?
2. Can you get to the desired service's port on that host?

For the first question, use this command:

    nslookup server.domain.name

That should not fail. You can copy and paste the command and output, and send it to your systems admin so they know whether you are getting the right IP. A lot of problems happen because DNS isn't working right, but that's usually easy to fix. You can also sometimes use `host server.domain.name`.

To answer our second question, about the port, suppose it's port 443:

    nc -vz myserver 443

That's a little-known command named "netcat", which you either have on your computer, or can install pretty quickly. Again, copy the command & results, and send it to your sysadmins. If you can't get to the port, there's usually a firewall or network routing problem - it might just be that the port is blocked for your computer and somebody needs to "poke a hole" for you.

Some people use `telnet` instead of netcat, and yeah, that works, but it tends to hang and get stuck (Ctrl-D might get you out), making people freak out and panic even when the connection is actually working. Netcat checks on the port and tells you, yes, it works, or no, I can't get there, and it's pretty clear about it.

If you don't even know what a "port" is, and it's your job to write software that uses a network, well, today is the day you need to go find out what a port is. If you are using services that insist on UDP, you may have to learn to use netcat with the UDP flag: `-u`.

## And now what?

Suppose the DNS & port checks come out fine, so now you're even more perplexed. It's possible the server might be overloaded to the point that it can't do anything other than accept a connection, and then it just "hangs", so that you sit there waiting for a long time whenever you connect. If not that, then very often you have a programming problem that has nothing to do with the server being down or up, in which case you're only going to annoy your sysadmin by dragging them into it. Still, if you give them the previous DNS & port information first, they'll know they can rule out a bunch of non-problems before deciding whether they have time to try to help you or would prefer to insist it's probably your fault.

## Ping

In case you're wondering, ping is a dumb way to diagnose the problem, because lots of servers have their ping service turned off, and you're not trying to connect to ping anyhow. Still, if you can't get nslookup to work, ping will usually report the IP, so that's another way to get it.

Another common command is `traceroute` (on Windows, it's `tracert`) but that's not necessarily reliable either. If a sysadmin _asks_ you to run either of these, have at it, and let them know what happens, but otherwise they probably aren't going to be helpful.

----

[Back to Software main page](./README.md)
