# Null and "Optional"

A lot of programmers get really excited about the type/class named `Optional` AKA `Option` AKA `Maybe` as it shows up in various programming languages: The null problem is solved! Hooray. As a matter of fact, Optionale et al is somewhat helpful in allowing an API author to clarify the potential missing-ness of inputs and outputs and thereby avoid accidents; however, it's worth asking what it means to be "missing"? While the possibilities are nigh-infinite, here are at least several legitimate but vastly different interpretations:

    - Not applicable (leads to many arguments about structure, but whatever)
    - Invalid (should be here, but isn't, so we're just going to deal with it as best we can)
    - Uninitialized (instantiation isn't always instantanteous)

Optional cannot accomodate the answer of "why is it missing?" unless there is only one relevant answer to be had. However, Optional itself is completely trivial to implement as a class/type/struct/etc in any language that supports such, and it's only slightly more work to add fields, flags and so forth to create an enhanced version that gives more information about *why* the information is missing.

Same goes for relational database design, where you might add a column or two as needed to explain why other columns are null. You don't need any special high-tech standard-feature enhancements for any of this, but it's always worth checking to see if there are any that might come in handy, like some sort of enum type.

The null problem is and always has been a very easy problem to deal with; the catch is that you have to _recognize_ whether you have such a problem, or not.

Yes, a lot of functional programmers would insist that we're missing the real excitement of Optional, which is its ability to act as a monad, which is a valid complaint except that overly loud functional programmers made the conversation about monads so convoluted that everyone else threw up their hands and gave up on it. That's too bad.

----

[Back to Software main page](./README.md)
