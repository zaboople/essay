# When Configuration Is Not Configuration

I have three rules:

1. It's not configuration if it's always the same.
2. It's not configuration if it's always the same.
3. It's not configuration if it's always the same.

If an application can resolve the answer to the question at compile time, don't replace that by answering it at runtime by reading meta-data and configuration files and goodness knows what. If there's a *chance* that eventually something might need to be configurable... No, that isn't good enough. There's *always* a chance, and you can deal with that chance when it becomes a reality, easily.

Probably the only exception (there's always an exception) is secrets, because putting secrets in source logic is capital-B Bad, even if it's the same secret no matter what environment you're in. Otherwise people can steal your secrets without even having access to your production equipment, just by getting to your source logic, so you have to lock down that source and establish draconian, paranoid security rules that constantly interfere with the development cycle - and don't blame your local security expert for that mistake.

----

[Back to Software main page](./README.md)
