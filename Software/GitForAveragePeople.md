# Git, For Average People

Git is controversial, because it doesn't seem like it was designed for average everyday dev teams, but everybody uses it anyhow. Many people will say, "The problem is that Git is too hard!" This might be partly right, but to be more specific, Git has a lot of things you will probably never need, and understanding *all* of them *is* hard. So: Don't. Remember the cardinal rule:

>  **Just because you _can_ doesn't mean you _should_.**

Crazy people don't care about that rule. They want to make simple problems into complicated problems, and will pressure you to normalize unnecessary practices and obscure Git commands. They will say things like, "Git is easy, if you just understand what a directed acyclic graph is," which is their way of making sense without making sense, to throw you off their trail. The way to deal with crazy people is to go *around* them, not fight them. This is easy when they're just random anonymous internet people, but harder when you have to work with them.

So for the average programmer at the average job, especially web application folks trying to get into a "continuous delivery" kind of workflow, the key is to just learn what you need to learn: How to branch, merge, push and pull. There's enough tutorials on those four already, but I'd like to cover some less-common territory above and beyond that. Keep in mind that rules are made to be broken, and I'm not even making rules here, just recommendations, YMMV and all that. Anyhow:

## First, Some Generally Good Practices

**Have a disaster recovery plan.** People might tell you that you can set up a new git server by just pushing your repo from a random workstation, but have you ever tried? One way or another, that server is eventually gonna get trashed, and you should be ready. Maybe you prefer server backup & restore, maybe not. Have plans.

**Get a trash repo to play with.** You can make one with a personal github account, or just ask your local sys admin to let you make one for the team. This way you can test out git tricks harmlessly instead of sitting with your shaky finger hovering atop the enter button, thinking, "Should I? Dare I? What if? Oh dear." Try out your idea on the trash repo until you're confident things are either okay or, "Nope." Your trash repo becomes the proverbial ounce of prevention/pound of cure.

**Avoid complex workflows if possible.** Your resident crazy person will want to make a test branch, production branch, patch branch, bugfix branch, staging branch, and a few more branches with weird names that don't make any sense; then they'll have a diagram with arrows going every which way showing the "right" workflow. Most average teams do just fine with a master branch that also acts as production, creating short-term "task" branches for their individual tasks; when a task is done, merge to master, deploy, and delete the task branch. That's how simple it can and often *should* be, and simpler is safer, by and large. If somebody wants to go beyond that, hold their feet to the fire and demand *reasons* for why the fancy workflow is *absolutely necessary*. Still, you may have to lose this fight, sometimes...

**Do not "force push".** When reading internet Git tutorials written by crazy people, you will often find a line like, "Now, here you might have to force push, but don't worry, it's not as bad as it sounds." No, force push *is* as bad as it sounds. Treat it like a deadly disease and avoid it. It's a great way to lose work and spend hours or days trying to recover.

**Push your branch to a suitable origin often.** This way you don't lose 3 weeks of work because your laptop got run over by a car, or fell in the lake, or was eaten by bears, or all of the above.

**Merge master into your branch regularly.** Don't wait until the last minute to find out that you have merge conflicts or subtle bugs introduced by new behavior that your branch didn't account for. (If master isn't your production branch, fine, then merge that production branch into yours, or merge master & production into yours, or whatever works).

    git checkout my_branch;
    git pull --ff-only;
    git merge origin/master;

**Rebase is the question - "No" is the answer.** Crazy people love rebase, because rebase allows you to _rewrite history_, and that's just so, so cool. Keep in mind that before Git, nobody even _considered_ rewriting history, usually because they couldn't if they wanted to, and also because they didn't care. Anyhow, a crazy person reviewing your merge/pull request will say, "There's a punctuation error in this commit message. You need to rebase and correct it." So you try rebasing, and git starts fighting you, and pretty soon you realize there's only one way to get this to work: Force push. Wait, hold on: Force push is a capital offense around here - no can do! There's a way around this, so read on.

## The Joy of Squashing

Let's say production just broke. Your first question is, "What changed?" Good question, so you look in the commit history, and unbeknownst to you, the actual change is in there with a helpful commit message, but it's timestamped as 2 weeks ago. Well, that can't be it then, can it? This broke today, not two weeks ago. No, actually, when you merge, you merge _histories_, so what was merged today *looks* like it changed days or even weeks ago, depending on when it changed in the source branch. What? Huh? Yep.

Worse yet, the change may have happened across several commits as someone try-try-tried again, and those commits are interspersed with the histories of _other_ people's work that was happening in _other_ branches that were merged into master at various _other_ times. Wait, come again? What? Ugh.

By default, Git merges the *history* of your changes, not the changes themselves. A lot of people intuitively think Git must be doing the latter, because that makes more sense to them, and thus merge confuses them. In fact, consider the history of your branch with all its backing and forthing, and all of that messy history getting merged into master even though nobody cares about it; no, what we care about is the *finished result*. So, consider *squashing*, which merges changes, *not* histories:

    > git checkout master

    # Let's merge my_big_branch *into* master:
    > git merge --squash my_big_branch

    # My changes are now staged, so it's up to me to do a final commit:
    > git commit -m "Here is everything I did in one great big commit"

Squash throws all of our branch's history away - commits, merges, everything. All of that gets "squashed" out. Instead our changes are now just a new, single set of changes in a vanilla commit straight to master, with a timestamp that reflects *when the change was merged*. Note that a lot of fancy-shmancy git-based products like github and gitlab support squash merge as an option for their review & approval workflows.

You might be skeptical about `--squash`, and ask, "If this is the better way to merge, then why isn't it the default?" Good question, and it wasn't my call, but for average programmers working on average teams, I'm telling ya: Squash is usually much, much better.

Note that when "backmerging", however, it's best to stick with regular old history merge. So when you're pulling down other folks' newly merged work from master into your task branch every day (as per best practices), you'd still do `git merge origin/master`. This way Git always has a solid reference point when comparing your branch to master later on.

In fact, it's worth pursuing a tangent here: What if we want to reuse our branch and do more work in it *after* we squash-merged it into master already? This is a questionable habit, but okay, first things first: Merge master into your branch, just to get squared away. When you do this, Git will think, "Huh, that's funny: It turns out my_big_branch mysteriously has all the same changes that were just committed to master. This programmer is psychic or something!" Git doesn't know that my_big_branch is where those latest changes came from in the first place, because squashing created a brand new SHA in master, and none of your SHA's match. Don't worry, though: We won't get a merge conflict as long as you haven't jumped the gun and already started changing your changes in my_big_branch. Backmerge first, and *then* you can change your changes (because you're indecisive like me).

That tangent kinda underlines the point of history merge: Instead of trying to decide the winners and losers of merge-conflict heck by comparing timestamps, Git just uses the history-merged SHA's to verify that you're already up to date with everyone else's changes (or not). We could also babble about directed acyclic graphs here, but you probably get the point already.

## The Joy of Revert

Squash provides an implicit bonus: Now that we know who broke everything and when, it's a lot easier to undo our changes because it's just a vanilla commit:

    > git revert <SHA of squash commit>

Heck, let's suppose we have to do that, but now... We're hesitant, right, because as soon as we press `enter` we're going to lose all of the work that we'd squash-merged before! Actually no, we aren't going to lose anything at all. Go ahead and press enter, deploy to production, then come right back to master and do:

    > git branch fix_my_mess
    > git revert <SHA of revert>

Yes! You can *revert a revert*, because a revert is just a vanilla commit in its own right. Wow! Now we just fix the fix_my_mess branch, and merge our way back to production for another try. Holy crap - is git *allowed* to be this easy? Maybe we should just keep this one to ourselves.

## The Rebase Alternative: Squash Again

Let's return to our problem of the peer review where someone is demanding we rewrite history to their specification. If we squash-merge our work into master, all those little commits become meaningless anyhow, but still, if people insist on a history-merge to master, we can fix our history like so:

    git checkout master;
    git branch just_for_crazy; # Make a new branch to submit for review
    git merge --squash my_original_branch;

Now we can submit the *new* just_for_crazy branch in an equally new merge request, but maybe our reviewer will be annoyed with us if there's now just one commit in our history. No problem: Instead of immediately committing, we "unstage" our changes (AKA "un-add"):

    git reset HEAD

Now we can selectively `git add` & commit different bits and pieces a few at a time, until we have a nice-looking history (yes, for some reason `git merge --squash` quietly does a `git add .` without asking us, and I wish it wouldn't). Our reviewer will of course spot the timestamps and be annoyed that it looks like we did two weeks' worth of work in five minutes, but so what? Too bad there isn't some git command to time-travel the reviewer themselves into some distant future, but then again, that would be inconsiderate (to people who live in the future, I mean...).

Overall, squash is a great way to start "anew" and shed the baggage of history in various situations. It ought to be easier to `--squash` one's real life as well, but... that's another story...

----

[Back to Software main page](./README.md)
