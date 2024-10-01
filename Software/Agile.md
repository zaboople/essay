# Myths of Agile Software Development

To understand "agile", one could start by reading the original [Agile Manifesto](https://agilemanifesto.org/) - it won't take long - but the general idea goes something like: Divide your work up into smaller parts, most important things first. Let stakeholders know what you're doing, get their feedback and review priorities with them regularly. Make adjustments to the plan if the plan isn't working. Make improvements as incrementally as possible instead of trying to throw a giant pile of stuff over the wall all at once. Keep a regular dialog going among team members so they know where the trouble is, who needs help, what is and isn't working.

These are very reasonable things, even if they're not particularly ingenious or "new". They're certainly not evil. Offhand, there's no good reason for everyone to hate agile, but it's become something widely despised by software developers, a catchphrase of loathing and dysfunction, and a symbol of the disconnect between management figures and the folks who work under them. We should try to understand why this is.

## The Manifesto

Looking further at the manifesto, it is actually divided into _two_ documents:

  1. The first is 4 rather vague, pithy introductory aphorisms.
  2. The [second](https://agilemanifesto.org/principles.html) is 12 brief principles specifying a little more detail.

The 4 aphorisms are the first sign of trouble, phrased in the style of _this over that_: Most notable are that "Working software over comprehensive documentation", and "Responding to change over following a plan". We might take this to mean:

  - You don't need documentation
  - You don't need a plan

Obviously, I am taking a statement about relative value and extending it to the absolute, which isn't what the authors intended, but it's commonly done. Agile proponents often have nothing to say about documentation, and see planning as a purely short-term endeavor, i.e. the next two weeks and little else. The Agile Manifesto doesn't _demand_ this kind of negligence; it implies that documentation and planning can be taken to an extreme where they become so voluminous and rigid that they are obstacles to change. The manifesto is _reactionary_, so it's focused on _pushing back_ rather than describing how to adjust your position to the right balance.

As usual, aphorisms are easily abused.

## Top-Down Agile

As the 12 principles go, we know most people never read them because of the 11th, which I'll quote verbatim: "The best architectures, requirements, and designs emerge from self-organizing teams." We can only interpret that as a requirement for _bottom-up_ organization, not _top-down_. Show me a software developer who hates agile, and I'll show you a software developer who had it forced on them, _from the top down_ by management, for better or worse.

But now we have a paradox: Management is completely bought in on the idea - surprise, it was an easy sell after all - but how to make the organization agile without forcing it on everyone? What if the teams just don't want to? The Agile Manifesto simply assumes they *will* want to, and if they don't, it shrugs. The manifesto wants to argue with management, not staff. I don't think the authors expected the argument to be so easy, or that the corresponding argument with staff would be so hard, even though the resistance is mostly passive: We're too busy doing stuff to organize and plan the stuff first.

So, "self-organizing teams" is a rare occurrence. Agile usually arrives within the organization as a bureaucratic mandate to reduce bureaucracy: top-down, appointed, enforced, with designated procedures, practices, methodology, training modules, forms, graphs, reports and very little overall interest in the actual work that must be done by actual individuals on actual problems. Most of this happens at the middle management layers, where folks attempt to translate upper aphorisms into middling formalities.

The manifesto has a second implicit assumption: Everyone is in a state of good faith, which is to say, people have no reason to distrust each other. They don't view one another with contempt. Once good faith is gone, agile is about as useful as a glass of water in a five-alarm fire, but this is usually where agile begins: Things aren't getting done, so let's "go agile". But in the absence of good faith, every tool is a weapon in an episode of intellectually violent warfare. Attrition is high indeed, and for anyone felled in such battles, it wasn't agile methodology that did you in; it's that your boss hated your guts.

Agile might seem like a way to fix organizational dysfunction, but that's not true because agile isn't a leadership system that fixes dysfunctional leadership. When _adopted_ by dysfunctional leadership, it simply becomes a weapon of dysfunction. Of course it doesn't _have_ to work out this way if we're consistently reinforcing good faith, but that certainly doesn't happen by default, or by accident. It's as if we're building a house without building foundations first. Our _project_ methodology has attempted to replace our _leadership_ methodology and failed.

While it's unrealistic to expect teams to self-organize in pure anarchy, it is realistic to appoint team leaders who force the organizing and are empowered to to structure their own process efficiently. These leaders certainly have to be accountable to those above them, but the accountability should focus more on *results* than on the process that obtains those results.

## Agile as Anti-Communication

Another proof that nobody reads the 12 principles is the 4th: "Business people and developers must work together daily throughout the project." "Daily" might be a little excessive as timing goes (sometimes weekly is sufficient), but it's much easier to get the stakeholder what they want if you can just _talk_ to them directly instead of filtering the conversation through an intermediary.

Many agile proponents actively seek to prevent this. If a business person is caught talking to a member of the software development team, they are lashed and beaten for trying to subvert the process and sneak requirements in "mid-sprint". Many proponents demand that a designated "product owner" act as guardian of the team, spear and shield in hand, daring stakeholders to step out of line and seek access they don't deserve. You end up with an anti-agile ethos in disguise.

Don't assume software developers are antisocial jerks who can't talk to "normal" people. Having a direct relationship to stakeholders is extraordinarily motivating for the team and defeats the tendency to see the stakeholder as fools who ask for nonsensical solutions.

## Agility Software

There are lots of software products that promote agility. These are basically "ticketing" systems for tracking work, with all the interesting workflow and tracking statistics that you'd expect. Most of these systems are very flexible, so that if you want to use them in the simplest way possible, you can; and if you want to tie yourself in knots, you can do that too. Guess what a dysfunctional organization is going to do with them?

1. Ticket forms can be customized, so that instead of a handful of fields - all you probably need, assuming one field is free-form text - you'll steadily grow to 10, 20, 30 and yes, even 50 different inputs for every single form, all completely disorganized and haphazardly arranged, all mandated by managers who are reacting to some special circumstance that they want to see formally addressed "from now on", all forgotten by same managers a couple of weeks later.

2. The software scales nicely, allowing you to enter thousands of tickets that will never go anywhere. Most of these tickets are incomprehensible, typed in by someone in a hurry, maybe dictated by another person in a hurry. The ocean of tickets assures we will never run out of work, but it also assures that we will always feel helpless and overwhelmed, and most importantly, prioritization becomes impossible. Once you lose priorities, "agile" is just a word in the dictionary. The ticketing system is now a reasonable approximation of infinity.

All of this comes down to the painful but necessary process of *culling*: Ticket fields need to be limited to some agreed maximum, so that new fields must replace old ones, or they don't go in. Furthermore, aging tickets need to be removed, which requires us to admit: "Yeah, nobody's ever gonna do that one," and/or "I don't even know what that's supposed to mean."

Don't give in to hoarding!

## Stakeholders

Most Agile advocates divide people up into *stakeholders* and *implementors*: Stakeholders want things, and implementors implement things. This dichotomy leads to a common fallacy that implementors *cannot* be stakeholders, which facilitates a "shut up and do what you're told" management strategy.

A stakeholder can be anyone with some kind of stake in the success of a software project. A corporate attorney might be completely disinterested in using the product, but they might also be the person responsible for telling us that we're doing something illegal, so: They're a stakeholder.

Stakeholders each have their own perspective on what the system should or shouldn't do, based on their knowledge and background. They don't carry weight based on rank and status, but on what they *know*. The lowest-ranking person may be the one person who recognizes critical deficiencies that must be addressed soon.

Who understands technical defiencies best? Technical people, i.e. first and foremost the implementation staff. Those technical staff are stakeholders too. Denying this fact allows us to deliver faster, but leaves us open to long-term hazards that the team knows about but is not allowed to work on.

## Efficiency

It's important to understand that formal processes don't magically make people more efficient; if anything, we'll be robbed of time spent on the process itself. Management is usually hoping their new agile initiative will fix the problem of "things not getting delivered on time", and they will usually be dissapointed. Then comes accusations that software developers could get the job done faster if they would stop working on the wrong things, so that we're really playing a game of Agile Crackdown: If you don't have a ticket for it, we better not catch you working on it.

Management may be right that things are not getting done quickly enough, but they need to consider alternatives to their assessment of *why*. Agile methods cannot compensate for bad software architecture that is choking software development. A process solution can only fix process problems. An "agile crackdown" tickets-required approach is actually reasonable, as long as we're recognizing the stakeholder-ness of the software development team (as per above) and balancing their priorities with those of everyone else.

## Risk

The Agile Manifesto doesn't have anything to say about risk, a glaring absence. Identifying, strategizing and resolving risk is one of the most important aspects of project management, and any methodology that ignores this is broken. This would have to be our most scathing criticism of the manifesto.

Of course the better Agile proponents are happy to embrace risk management, and the manifesto is basically compatible with such, but dysfunctional project managers will try to resolve risk by avoiding it. They will seek to delay implementation of risky work until failure is otherwise imminent, so they can keep getting paid until then. They will dismiss any worries as "lack of a positive attitude" because they in fact have a _negative_ attitude: Of course it's going to fail, but let's get paid first!

You need to make risk management a core component of your methodology, not a thing that your method merely tolerates.

## Short-Term Thinking

Most Agile interpretations focus on some idea of "sprints". The sprint is central topic of conversation: Can this be done in one sprint? Will that be done by end of sprint? We must have a "demo" for the end of sprint - what can we demo? None of this is bad per se: Sprint-based planning is simply a matter of dividing & conquering across time. No matter how big the job, you have to start _somewhere_.

The Sprint-based approach has no conflict with medium-to-long-term strategy, but it has absolutely nothing to say about them either. However, long-term thinking is hard, and nobody wants to do the hard parts, so we can just declare: Agile says long-term thinking doesn't count! That's simply not true.

## Requirements, Security and Architectural Risk

A while back I was working on a project with the kind of customer that has to be really paranoid about security, just because their business was the infrastructural kind that makes sure you have water, electricity, gas, etc. You don't want that infrastructure to fail because terrorists, nation-state actors, criminal blackmailers etc. took advantage of a security failure.

Is it likely this kind of customer has a department of information security and written policy about how new systems must be designed? Yes, but this was an "agile" project, and it followed the implied standard practice of, "All requirements will be accidents." So there was no need to collect security requirements, even if they were readily available, because people felt that wouldn't be agile; we should build the software first, and then find out if it meets requirements, and if that means a lot of work being redone, so be it.

By and large security requirements are architecture requirements, and architecture requirements are scorched-earth requirements: Fail to pay attention and you'll end up gutting your system and starting over. That's exactly what happened, and then everyone started feeling not-very-agile. The project died, so at least it didn't cause security problems after all.

Most managers and software engineers are used to demanding full control over the architecture: How dare you tell us how to design *our* system? In fact this kind of scenario is becomingly increasingly common, so that corporate & governmental customers are often defining architectural security requirements up front.

Sometimes internal architects and security professionals are left in the dark, only to appear late and start making their own demands. Systems and network administrators may veto a decision as well. This is another example of our technical staff acting as *stakeholders* with *requirements*. Consider the plight of the junior software developer, who is subject to the whim of anyone who outranks them, even on their own team; they are at even greater risk than usual of a rug-pull.

I always tell people, "Requirements are the part of the design that is required." This makes a lot of software developers angry, because it seems to blur a line that they consider solid and unbreakable, but pragmatic reality disagrees. So, you're allowed to say, "Obviously C++ is the only reasonable language to use here." You just need to recognize that when someone else is implementing the system, you have a requirement that they need to know about.

Architectural requirements should be identified as early and quickly as possible. Ad-hoc discovery will result in massive setbacks and potential failure.

## Conclusions

Agile recognizes values out of balance, but as per the 4 principles, it demands a reversal of values rather than balance, and that is a mistake. Yet where the manifesto gets things right, proponents just ignore it and project their own agenda onto it, because the manifesto is so vague that distortion is easy.

The Agile Manifesto is rather reactionary, and it's reacting _against_ bureaucracy. It lashes out at restrictive hierarchies, excessive paperwork, and vaguely titled individuals who opportunistically insert themselves between people with questions and the people who can answer them. Bureaucracy is both a solution and a problem, and people have been trying to solve that problem throughout the history of civilization. It is an oversimplification to just declare one's self "anti-bureaucracy". Who is "pro-bureaucracy"? Nobody I know, but organizations have to organize, and that's harder than just blurting pithy aphorisms.

Some final, general recommendations for embarking on agile:

1. Restore good faith as needed, because otherwise Agile falls apart. This requires enough honesty to identify the bad faith and even harder, its source - technical or personal - and that's bloody work.

2. Believe in belief: If the process is promoting efficiency, the team should believe in it, but are teams allowed to say that they _don't_? It doesn't matter what the graphs, books and agile coaches tell you; if the team believes the process is pointless, the process has failed, and shutting them up doesn't change that.

----

[Back to Software main page](./README.md)
