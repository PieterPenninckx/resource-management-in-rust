Summary
=======


Resources are things like files, memory, etc. 
They are managed by a resource manager that gives you the possibility to acquire
a resource and when you don't need to use the resource anymore, then you can
give it back.
This handle is stored in an owner: a value.
A value is a piece of memory that has an associated type.
Not every value contains a handle that corresponds to a resource.
If a piece of memory does not contain a handle, then you can opt out of move
semantics.
Move semantics means that you cannot make a duplicate of a variable: when you
copy it, the original cannot be used anymore.
Borrowing is specific for memory: you borrow a value by taking a reference to
it. A reference is just a memory address to a value.
This reference is only valid for as long the value can be used.
There are two types of references:

reference as immutable & as mutable.
When you have access to a value, then you can be sure that no-one else has.
