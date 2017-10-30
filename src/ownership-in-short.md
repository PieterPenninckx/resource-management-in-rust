Ownership is the responsibility to release the resource
-------------------------------------------------------

We say that some piece of data owns a resource when it contains within its data
structure a handle to the resource and additionally it has the responsibility
to release the resource after use.
The resource can be of any type: a piece of memory, a file, or anything that
your operating system might offer.

Remember the exercise at the very beginning where I asked what kinds of errors
the programmer can make?
Rust's ownership concept prevents (among others) the following two problems:

1. using an uninitialised handle that does not really correspond to a resource,
2. using a handle after the resource has already been released.

The first problem is prevented by ensuring that an owner can only be created
after the resource has been acquired. E.g. a program can only instantiate a
variable of the type `File` after it successfully opened or created a file.

The second problem is prevented by automatically releasing the resource
corresponding to the owner when the owner goes out of scope and by having no
other means to release the resource.
Data types that directly store a handle need to implement the `Drop`
trait that offers the `drop()` function.
(Data types that only indirectly store a handle don't have to, see below (TODO:
write that section).)
The `drop()` function asks the resource manager to release the resource 
and cannot be called directly.
The compiler automatically inserts a call to the `drop()` function when
the owner goes out of scope.

Both mechanisms rely both on language features and on correct API design (
e.g. of the API that offers the `File` data type).
We will [illustrate the importance of correct API design](./an-example-of-providing-a-resource-in-rust.md) below.
