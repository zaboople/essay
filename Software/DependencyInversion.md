# The Dependency Inversion Hazard

Narrative is an extremely underrated concept in software engineering. Your programming logic is telling a choose-your-own-adventure story, where a narrative twists and turns according to various inputs, and you must Write To Be Read. Some of us will recall the act of trying to revisit something we wrote a year ago, and struggling to read our own work: What the heck was I thinking? Writing to be read is challenging work. Have you ever attached a debugger or started shovelling println()'s every which way in an effort just to find out: *Where the heck does it go from here?* Or forced a stack-trace dump just to find out: *How the heck could we land here?*.

Popular platitudes often ignore the cognitive overhead of discovering the narrative. Dependency Inversion is a favorite example: We are instructed to replace method calls against "concrete" classes with method calls against abstract classes, interfaces, lambdas, etc. Nobody warns about the hazard: When you are trying to discover the narrative in dependency inversion, you are going to slam into dead-ends where you have to reverse-engineer your way backwards from abstract to concrete, getting stuck in the mud and winching your way back out. Do this often enough and your task becomes discouragingly distracted and time-consuming.

Dependency Inversion is still a necessity for at least two reasons: It allows you to test one part of a program without booting up everything else, and it allows you to create reuseable libraries that feature callbacks as input. Naming and organizational conventions can help somewhat with tracking down concrete logic. Knee-jerking dependency inversion as a universal platitude is bad form, however. Think of it this way: *You need concrete reasons for your abstractions.*

----

[Back to Software main page](./README.md)
