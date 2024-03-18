# Constants At The Top

I've noticed that many programmers insist on a rule that Numeric/String literals (i.e. "hard-coded") must *always* appear at the top of a file as assignments to constants, even when only used once. This often means constant (that's a pun) back-and-forth for a reader who has to track down the definition of each constant they find, making the effort to read the logic even harder for the sake of dogma.

This is most noticeably burdensome when embedding lots of SQL logic, so that each SQL query is way up top - because it's a string constant - and the logic referencing it is waayy down below. Arguably it just increases the likelihood of mistakes because we can't remember things like how many parameters are in our parameterized query and in what order, which columns are selected into our output dataset, and so on.

The original idea is if we have multiple instances of logic that depend on the same constant value and that will break if the constant value is inconsistent between them, then we should attach that constant value to a variable and dereference the variable instead of referencing the constant value directly, to avoid breakdowns. Like so:

    const XXX=222

    // ...

    if (a - b == XXX)
        // ...

    if (a | b == XXX)
        // ...

    if (a % XXX == 0) // And don't tell me you want "0" defined as a constant!
        // ...

Our constant-based approach is reasonable here, because so many things depend on the magical number 222. It's worth the distraction of having to scroll up and find out what XXX refers to. If 222 only gets used once, the value dissapears, although we might excuse it occasionally if we think the 222 is likely to appear in other places very soon.

----

[Back to Software main page](./README.md)
