Garbage collectors
------------------

In the previous section, we illustrated that in many programming
languages, the programmer does not have to explicitely write instructions to
release memory is no longer needed. 
But if the application were to only reserve more
and more memory, it would soon run out of memory. So ultimately, somehow the
application has to know that some pieces of memory are not used anymore and
may be re-used for something else. 

This is the task of the garbage collector. 
The garbage collector is a piece of software that 
runs together with your application.
It analyses the state of the running program, looking for pieces of memory
that can no longer be used.
It then releases these pieces of memory.

There are many techniques for garbage collecting.
One technique, called 'reference counting', can be implemented relatively easily
and requires only a little help from the programming language.
Other techniques require extensive support from the programming language.

Most recent programming languages, including Python, Javascript, Java, C# and 
Go, offer extensive support for garbage collecting. In these languages, you 
typically rely on the garbage collector for nearly everything you do, as is
illustrated in the previous section.

C++, Rust and Swift have enough features to implement reference counting, 
but do not offer any support for garbage collection beyond that. 
For this reason, it is often said that these languages do not have garbage
collection, although technically speaking, that statement is not correct.

Reference counting is not the most important technique to manage memory in Rust,
however, and it is typically not used all the time. 
Depending on the type of software you are writing, you can even write a complete
program and then realize that you never used reference counting.
