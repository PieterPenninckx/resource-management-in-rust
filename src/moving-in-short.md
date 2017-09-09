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
