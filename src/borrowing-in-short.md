Borrowing is having access
--------------------------

If you borrow a value, then you store the memory address of that value in
another value.
A value that contains a memory address of another value is called a _reference_
to that value.
You can only borrow a value for as long as the borrowed value is valid.
So if your value corresponds to a resource and has move semantics, than the
reference to that value is only valid until that owner is either moved or goes
out of scope.
This is also checked at compile time.

There are two kinds of references that can be used for borrowing:

* references as mutable
* references as immutable.

A reference as immutable can only be used for read access to that piece of memory

A reference as mutable can be used for writing as well.
You typically want to keep track of what the state of your data is.
If you have a reference to a piece of memory and this piece of memory changes
behind your back, than this can break an invariant that you have in mind, 
like "if this is > 0, then also that is >0".

So for this reason, if you have any reference, then you can be sure that no-one
else has a reference as mutable to that piece of memory.
So if you are the owner and you can use a value, then you know that there is no
reference to it.
For the period of the borrowing, the original value is frozen, which means that
you cannot do anything with it.

TODO: Explain "locked" (values are locked when they are borrowed as mutable,
which means that they cannot be read from).

### Borrowing and ownership

There is a link between ownership and borrowing:
the thing that is borrowed, can be an owner of a resource.
When this happens, the borrower has the memory address of the owner,
and via this, it could access the handle, and in this way, it could then use the
resource.
Concerning terminology, we simply say "borrower of the resource" instead of
"borrower of the owner of the resource".

To keep things under control, the owner of a resource needs to have control
over what its borrowers can do.
For this reason, the borrowers cannot access private fields of the owner.
In particular, they cannot read or manipulate the handle that gives access to the
resource.
But the owner can provide methods to the borrowers to perform certain actions
on the resource.
For instance, if the resource is a file, then the code that is associated to
the owner may provide functionality to read data from a file or write data
to a file.
It can distinguish between borrowers that borrow as immutable and borrowers
that borrow as mutable.
For instance, you might want a borrower that borrows as immutable to be only
able to read from the file and to grant access to write to the file exclusively
to borrowers that borrow as mutable.
