Moving is transfer of ownership
-------------------------------
Quite often, you don't want to limit using a value to a specific part of your
code, you want to pass it to another function etc.
But you don't want to duplicate the handle, because then you can't know which
copy is responsible for releasing the resource.
For this reason, Rust disallows using the 'old' handle when it has been copied to
somewhere else.
Hence it's also not called "copying", it is called _moving_ instead.
We say that the value that holds the handle has _move semantics_.
This move can be done by a simple variable assignment.
When you assing a variable to a new variable, the new variable is the owner
of the resource.
The old variable still holds the handle, but it cannot be used anymore: the
compiler disallows this and the `drop()` function will not be called anymore
when the old variable goes out of scope.
The new variable is the owner and is responsible for releasing the resource.
Either it transfers the ownership to yet another new owner, or it releases the
resource when it goes out of scope.

### Not all memory owns a resource

Not every value really corresponds to a resource that is manager by a resource
manager.
For instance, a value may hold a number that has been entered by the user, 
or a number that is a result of a calculation.
This does not need to be required or released, so it is no problem at all if
this value gets copied many times and each copy is used (e.g. in a calculation).
So not all values have move semantics: you can opt-out for it and choose for
copy semantics.
Data types can opt out of move semantics when they do not contain any handle
that corresponds to a resource.
We then say that the data type has _copy semantics_.
