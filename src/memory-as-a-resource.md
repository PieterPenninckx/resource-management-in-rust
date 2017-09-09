Memory as a resource
====================

Memory is a resource just like any other, but in all programming
languages that I know, it is handled differently. 

One, quite obvious, difference is that in many programming languages, memory
does not have to be released explicitly because a garbage collector takes care
of that. Although Rust does not rely heavily on garbage collectors, I will spend
some time explaining garbage collectors.

A second difference is more subtle and we will discuss it later on.

But first, let us start with some examples of how memory is used in programming
languages you are probably more familiar with.
