Ownership is the responsibility to clean up
-------------------------------------------

Ownership means having a handle that gives access to a resource.
This can be any type of resource: a piece of memory, a file, or anything that
your operating system might offer.
This handle is stored in a value.
A value is a piece of memory, but not just any piece of memory: a piece of memory
of which you know what it is supposed to store.
Rust is a typed language: any value has a specific data type: so that you know
if it stores an integer, a struct, enum, utf-8 encoded string or whatever.
This data type gives the application at compile time insight on what this value
can be used for.
When a value is an owner, this means that it contains a handle that gives access
to a resource.

In order to keep things easy, creating a value that acts as an owner, is the
same acquiring the handle that gives you access to the resource.
When this value goes out of scope -- directly , because it's a local variable
and the function ends, or indirectly because it's stored in a piece of memory
that is released -- then the `drop()` function is called on that specific type
that corresponds to the value.
This `drop()` function asks the resource manager to release the resource.
This is part of how Rust ensures that resources cannot be used after you have
released it.
