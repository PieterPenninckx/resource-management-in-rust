Some core concepts illustrated
------------------------------
We can use this simple example to introduce some core concepts of the
Rust programming language.

First, because the variable `file` stores a handle that corresponds to the file
we opened, we say that it is the _owner_ of the file.
The variable `file` is responsible for closing the file.
This will happen when the variable goes out of scope.
In general, any resource owner is responsible for releasing the resource it owns.

TODO: explain _borrowing_ with the example.

TODO: create another example that illustrates _moving_.
