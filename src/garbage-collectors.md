Garbage collectors
------------------

In the previous section, we illustrated that in many programming
languages, the user does not have to explicitely indicate that a piece of
memory is no longer needed. But if the application were to only reserve more
and more memory, it would soon run out of memory. So ultimately, somehow the
application has to indicate that some pieces of memory are not used anymore and
may be re-used for something else. 

This is the task of the garbage collector. 
The garbage collector is a piece of software designed only for this goal
and it runs together with your application.
In order to know which pieces of
memory may be released, the garbage collector analyses the state of the program
as it runs. 

There are many techniques for garbage collecting.
One technique, called 'reference counting' can be implemented relatively easy
and requires only a little help from the programming language.
Other techniques require extensive support from the programming languages.

Most recent programming languages, including Python, Javascript, Java, C# and Go
offer extensive support for garbage collecting. In these languages, you 
typically rely on the garbage collector for nearly everything you do.

C++, Rust and Swift have enough features to implement reference counting, 
but do not offer any support for garbage collection beyond that. 
For this reason, it is often said that these languages do not have garbage
collection, although technically speaking, that statement is not correct.

Reference counting is only one technique to manage memory in Rust, however, and
it is even not used that often. Depending on the type of software you are
writing, you can write a complete program and then realize that you never used
reference counting.
