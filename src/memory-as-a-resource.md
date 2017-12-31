Memory is a special kind of resource
====================================

Troughout this document, except when specified otherwise, I use 'memory' to
refer to RAM (and maybe disk when swapped out).
This excludes CPU registers, which are also a kind of memory, buw which you will
typically only encountered when programming in assembly.

Most, if not all, programming handle memory differently than other resources.

One, quite obvious, difference in many programming languages is that memory
does not have to be released explicitly because a garbage collector takes care
of that. Although Rust does not rely heavily on garbage collectors, I will spend
some time explaining [garbage collectors](./garbage-collectors.md).

A second difference is more subtle and we will discuss it later on
(see section 
'[Not all memory needs to be acquired](./not-all-memory-needs-to-be-acquired.md)').

But first, let us start with some examples of how memory is used in some of
today's most common programming languages.
