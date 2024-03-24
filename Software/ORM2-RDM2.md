# The Kingdom of the Riches of Domain Models: A One-Act Play

Narrator: The collective "we" has been asked to implement a new business process involving payments and invoices, and we've gone out in search of answers. Our first encounter is with Invoice Person, who authored the famous Invoice class, which mirrors invoice data from our database. Let's see what he has to say...


Invoice Person: Okay, so what you need to do is put this in the Invoice class, see? You make a method called Invoice.apply(payment). See how _natural_ that is? It practically flows from your fingertips, it's so intuitive.

Us: Oh, yes. We like the sound of that!



Narrator: But Payment Person has something to say...

*Payment Person walks in and looks around.*

Payment Person: Harumph! What is going on here!

Invoice Person: Oooh, nothing, nothing at all...

Us: We're applying payments to invoices!

Payment Person: WHAT? Why was I not consulted about this???

Invoice Person: Ahem. It's really -

Payment Person: Nevermind! I'm here now. So, here's what you do: Write the logic in a new method named `Payment.applyTo(invoice)`. See how natural that is? Makes perfect sense. Flows right from your fingertips!

Invoice Person: Now hold on just a second there!

Payment Person: _You_ hold on a second!

Invoice Person: You're not the boss of me! Besides, if the logic goes in the Payment class, then we have to expose all sorts of private information from Invoice to Payment, _Things That Should Not Be Visible_!

Payment Person: No, if the logic goes in the Invoice class, we'd have to expose all sorts of private information from Payment to Invoice, Things That -

Invoice Person: That's stupid! I already said that!

Payment Person: _You're_ stupid!

Us: Oh my!



Narrator: Behold! The king approacheth!

_Along comes King Solomon, looking irritable_

    King: You people are disturbing my slumber and I'm completely fed up now!
    Invoice Person: But -
    Payment Person: But -
    King: SILENCE! I decree that the logic will be split in two with my mighty sword of kingliness, and half will go to you, the other half to you! So be it, and all that! *Waves hands ceremoniously*
    Invoice Person: Sigh. Ok.
    Payment Person: Fine. Whatever.
    Us: Alrighty then!

_Along comes the Customer Person, wearing VR goggles for some reason, and thus a bit stumbly_

    Customer Person: What is going on here?
    Invoice Person: Um...
    Payment Person: Nothing!
    Us: We're going to apply payments to invoices!
    Customer Person: AND I WASN'T CONSULTED WITH? Everyone knows _I_ am the Customer Person!
    Payment Person: Well it's already settled and decided, and it's too late for -
    Customer Person: Now see here: _Your_ piddly Invoices and _your_ Payments are just inanimate objects! They're _virtual pieces of paper_, for heaven's sake! Pieces of paper don't _do_ things! They just _sit_ there!
    King: Sigh...
    Customer Person: See, now, my _Customer_ objects, they _do_ things! They are people, complicated people who live in my richly rich and virtual matrix world of high-tech virtuality and do all sorts of things, things which must be carefully modelled in where else but the all-important Customer object! There is no other object-oriented way!
    Invoice Person: NO! That's not fair! You're _always_ trying to hog all the logic!
    Payment Person: Yeah! Your stupid class is 10,000 lines long already and it's practically unmaintainable!
    Invoice Person: Here comes the merge conflicts!
    King: SILENCE! Alright, I've revised my decision: We'll split it THREE ways!
    All: Aw, man!
    King: SHUT UP! Now, it's a fair argument you have, there, Customer Person, so I'll allow you to _initiate_ processing, and dole out responsibilities - in equal thirds, mind you - between yourself and the other two.
    Customer: That's what I'm talking about! Customer Person always runs the show, yeah! *(pumps fist and stumbles)*
    Invoice: Dammit.
    Payment: Phooey.
    King: And that's final, no further disruptions, since I have an appointment with my masseuse at noon in the main conference room.
    All: Ew.
    King: I'll try to remember to pull the shades this time.
    Invoice Line Item Person: HEY EVERYBODY!
    All: GO AWAY!

_King Solomon exits stage left, while the crowd begins fist-fighting._

_The End_

# Sorting out the Modelling & Simulation Riot

## Exposing state

It's important to stop and understand the difference between:

    doSomething(invoice)  // The "anemic" variation
    invoice.doSomething() // The "rich" (OOP) variation

The first line forces `invoice` to expose all necessary state "outside" itself; the second makes it easier to keep that state private, reducing its scope, and limiting scope is generally a good thing. But what about:

    invoice.doSomething(payment)
    payment.doSomething(invoice)
    doSomething(invoice, payment)

In all three cases, either the invoice or payment or both will have to expose some internal state, and in that light, it might as well *be* both. Have we just invalidated the whole idea of object-oriented scope control? No, we're just recognizing that state has to be available where it's needed, and the *business process* needs it.

Consider the various stateful indexes, arrays and so forth inside a list, hashtable, tree, etc.: This background-recordkeeping sort of data is easy to hide inside an object-oriented class and keep strictly private because it's just not useful outside of the enclosing data structure's scope. What about the actual information that you put *into* that list or hashtable as an everyday user-programmer? Obviously the whole point of the list/hashtable/tree is to make this *other* data easy to manage, not to lock it away where nobody can get to it.

Database data generally doesn't fit the mold of background data-structure recordkeeping; it's useful in its own right. You will design and/or use data structures to make payment and invoice data easy to sift, sort, and otherwise manipulate, but it makes no sense to try to bury the payment & invoice details deep underground when you'll only have to dig them back up again and again.

Put simply, just because *some* data fits into an object-oriented class handily, that doesn't make it compelled to follow the same rules as *other* data that also fits into O-O classes. To insist otherwise is to abuse and darned near torture the art of analogy.

## Some Things Don't Do Anythings

Customer Person is correct in asserting that invoices and payments are just virtual pieces of paper - documents - that never did anything in "the real world" and certainly don't need to _do things_ now. They are simply information. They are not animals, or even machines.

But what about customers then? In our architecture, are customers puppets, with us pulling strings and making their arms & legs wave around? Actually, no. The customer is external to us, and we don't control them. Our job is to receive commands from the customer, do our best to carry those commands out, and properly account for everything. Tracking customer actions is not the same as _simulating_ them. So what we might call a "Customer Object" is merely the information we need to know about the customer, not a behavioral simulation.

We _could_ build a customer simulation engine for testing, with little customer puppets (so to speak) that _do_ things and try to buy stuff from our system. We'd need to model customer behavior in our little puppets as realistically as possible, and maybe even hyper-realistically, so that a lot of our customers are criminals, institutional patients, dogs, small children, and so forth, to verify that our system can tolerate any and all abuse. But this is a whole different thing, and if you're jealous, maybe you should've taken that job in quality assurance that you turned your nose up at.

## Divide and Conquer Is Just a Heuristic

It might seem that our Solomon-ian strategy of doling out bits and pieces of responsibility to whatever objects happen along consitutes a divide-and-conquer strategy, but divide-and-conquer only works when the separate parties can act with true independence, and this is unlikely when we are implementing a transactional process of *relationally* (as in RDBMS) related steps. We'll only end up with a confusing game of hopscotch where we have to chase a process from file to file to file to file to file.

We know we're using the Rich Domain Model, in fact, when we have so many files open in our editor, we can hardly keep track of them, and yet we're only trying to understand a single narrative. We'll end up with a book-of-the-month club where each book is actually other books chopped up by chapter and reassembled haphazardly; to derive a coherent narrative we'll have to wait until all the books arrive and open them all at once. We've actually *rejected the narrative itself* and demoted it to second-class status.

From an art-school standpoint, this is certainly very artistic and postmodern, but from a business standpoint, well, William Burroughs was definitely not a competent software engineer, and apparently art school didn't pan out for you anyhow...

## There Is A Simulation In Here After All

If we're looking for a "virtual" being that implements _behavior_, what we really have is a "virtual accountant". This accountant encounters payments and attempts to apply them to the most appropriate invoices. Rather than dividing up the logic across a bunch of database-record mirror objects, we would put it all in our virtual accountant, and thus end up with something like:

    VirtualAccountant.apply(payment) // Find the most appropriate invoice and apply payment

or:

    VirtualAccountant.apply(payment, invoiceCollection) // Apply to these invoices

- and maybe a few other variations. We're free to decide that this virtual accountant is strictly concerned with payments, and have some other virtual accountants that specialize in other, mostly unrelated things. We might have certain kinds of virtual accountant that specialize in _shared_ logic, like `va.makeInvoiceForOutstandingBalance(items)`, since all sorts of different business processes share the same ending: an invoice. Now we start seeing a much more coherent narrative, where our text files form outright *narratives* instead of jumbled collections of bits and pieces.

Ironically enough, in this world of objects, the Virtual Accountant is the one thing that _doesn't_ need to be  "persistent" (stored in the database). After all, did it ever make sense to put actual people in filing cabinets to deal with later? If you think so... we need to talk...

Does the virtual accountant even need to be object-oriented? It certainly _can_ be, without harm, but any real necessity depends less on aspects of "simulation" and much more on how OO helps us create a coherent process with any necessary state included, as we write it. OOPS is a tool in our toolbox that we can use whenever it makes sense, as usual.

## But You Just Won't Stand For It

Some so-called engineers just won't budge on their Rich Domain Model dogma, leading to a problem: Data is read here, written there, read some more here, more there, and it's impossible to count how many reads and writes we've incurred; but more importantly, unintentional read/write _overlap_ is going to happen, resulting in lost information. This is an excellent argument for establishing a *coherent narrative*, but since you've rejected that, you're likely to blame the ORM for not fixing it. More on that [here](./ORMsFail.md).

Disaster ensues, because . So you'll spend several years blaming your ORM angrily for being so stupid, then give up on your career in frustration, just in time for the next idealist-elitist to come along and do the same thing, until everyone decides to blame object-oriented programming for their own mistakes and go back to writing assembly.

Eventually we'll all come to terms with this, but that's a ways off.

