Resources in Rust
=================

Rust has a uniform system for memory and other types of resources.

Introduction example
--------------------

Let us give an example illustrating how you can write to a file in Rust,
similar to the example in pseudo-code we gave at the very beginning.

For ease of reference, we copy the example in pseudo-code here.

```
WRITE_TO_FILE()
1   file_descriptor ← OPEN_FILE("MyDocument.txt")
2   text_to_write ← "Hello, World!"
3   WRITE_TEXT(file_descriptor, text_to_write)
4   CLOSE_FILE(file_descriptor)
```

In the pseudocode example we ignored the error handling.
In real Rust code, we cannot ignore errors that occur.
This makes the code longer.

```rust
use std::fs::File;
use std::io::prelude::*;
use std::io::Error;

fn write_to_file() -> Result<(), Error>{
    let mut file : File = File::create("MyDocument.txt")?;
    let text_to_write = b"Hello, World!";
    File::write_all(&mut file, text_to_write)?;
    
    return Ok(());
}

```

The first three lines are just there so that the
compiler needs what is meant by `File`, `Error` etc.

In the first line in the function body, the program asks the operating system
to create a file named "MyDocument.txt". The resulting file descriptor is wrapped
in a value of type `File` and stored in the variable named `file`. The question
mark at the end of the line is for error handling and indicates that the
function returns an error when the file cannot be created.

The second line in the function body defines the data we want to write.
The `b` before the opening double quote indicates that the string is an ASCII
string. The only reason for this is to simplify the example.

Line three of the function body can be written more shortly as follows:

```rust
	file.write_all(text_to_write)?;
```

Again, the question mark is for error handling.
You can ignore the `&mut` for now, you will encounter it many times later on.
In essence, it is just as in the pseudo-code example: we pass the handle
(the variable `file`) and the text we want to write to the a function that
asks the operating system to do the real work.

The `return Ok(());` is again for error handling and it indicates that no
error occurred.

## Resources used in the example

Apart from the memory that the application needs to store its CPU-instructions,
it also needs the following resources when executing the function 
`write_to_file` described above:

* A piece of memory to store the the variable named `file`.
  Because this is a local variable, it is stored on the stack.
* The file name to be passed to the function `File::create`. Because this is a
  constant, it is stored in static memory.
* The file name needs to be passed to the function `File::create`. In fact, the
  function `File::create` does not expect the file name itself as a parameter,
  but only the memory address of the file name and the length.
  Because the file name is stored in static memory, its memory address is in 
  fact a constant (TODO: this needs to be fact checked) and this constant can 
  be stored as part of the CPU-instructions of the application.
  Also the length of the file-name is a constant.
* When the function `File::create` is called, the function `File::create` also
  needs some resources. For instance, it stores its parameters (in this
  case the memory address and the length of the filename) on the stack while
  it executes.
* Of course, the open file itself is also a resource used by the
  application. A handle to this file (a file descriptor) is stored in
  the variable `file`.
* The text that will be written to the file. This is again a constant, so it
  is stored in static memory.
* The function `File::write_all` expects the memory address of a 
  variable of type `File`. This memory address is notated as `&mut file` 
  in the example. In fact, `&` means "take the memory address of".
  The `mut` indicates that the function `File::write_all` has exclusive
  access to this memory address while it is executed. This will be explained
  in further detail when we talk about "borrowing as mutable".
* The function `File::write_all` also expectsthe memory address and the length
  of the data to be written. This is similar to the filename parameter of the
  function `File::create`.
* The caller of `write_to_file` will store the return value of
  `write_to_file`.
* For completeness: when the function returns, it needs to know where it has to
  return to. This information is also stored on the stack.

TODO: explain step by step what happens when the program gets executed,
including when the file will be closed.

We can use this simple example to introduce some core concepts of the
Rust programming language.

First, because the variable `file` stores a handle that corresponds to the file
we opened, we say that it is the _owner_ of the file.
The variable file is responsible for closing the file.
This will happen when the variable goes out of scope.
In general, any resource owner is responsible for releasing the resource it owns.

TODO: explain _borrowing_ with the example.

TODO: create another example that illustrates _moving_.

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

The owner of a resource stores the handle in a part of its data structure.
The handle is enough to communicate with the resource manager.
So the owner doesn't want this handle to be used by any other part of the
application, because then any other part of the application might for instance
tell the resource manager that the resource is not needed anymore.
For this reason, the handle is stored in a part of the data structure of the
owner that can only be accessed by code that is associated directly to the owner.
You can think of it as being a private field.

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

Borrowing
---------

Borrowing is really specific to memory, it's not for resources in general.
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

TODO: Needs to be restructured

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

Borrowing and ownership
-----------------------

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

Recap
-----

Resources correspond to a capability of the hardware.
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

Annotated code example
----------------------

Phew, that was a lot of theory.
Time to illustrate this with an example!
Rust's file system API is a little bit too complicated to work as an introduction
example, so let's consider a simplified version.
Don't worry if you don't understand everything, we will explain every concept
(related to references and borrowing) in detail further on.

```rust
mod FileSystem {
	pub struct File {
	  	file_descriptor: u64
	}
	// Incomplete code, to be continued.
```
Here we start a new module, named `FileSystem`.
The module acts as a boundary that restricts access to data types, fields, and
functions.

In the module, we declare a new data type, named `File`. 
Remember that Rust is a typed language.
The data type is a struct, which means "something with fields".
The `pub` keyword before `struct` indicates that 
the struct is public, which means that it can be used outside the module 
`FileSystem` in which it is defined.

The struct has one field, called `file_descriptor`. 
This field will be used to hold the handle inside an `u64`.
There is no `pub` keyword before the name of the field, so the field can
only be accessed from within the module `FileSystem` in which it is defined.
In other words, it's a "private" field.
	
An `impl` block associates data to a given type.

```rust
	// Still in the module `FileSystem`.
	impl File {
		pub fn open(filepath: &FilePath) -> File {
			// `ask_operating_system_for_file_descriptor` will be defined later
			let file_descriptor 
				= File::ask_operating_system_for_file_descriptor(filepath);
			return File{file_descriptor: file_descriptor};
		}
	}
	// Incomplete code, to be continued.
```

We define a function called `open` that takes a parameter called `filepath`.
The ampersand before the type `FilePath` indicates that `filepath` is a
reference to a value of type `FilePath` itself, rather than a value of type
`FilePath`. (We omit the declaration of the type `FilePath`.)

The function returns a value of type `File`. For simplicity, there
is no error handling and we just let the program crash by calling
`panic!`. Of course, the the standard library has proper error handling.

The `pub` keyword before the name of the function indicates that the function
is public, in other words, it can be called from outside the module 
`FileSystem` in which it is defined.

Internally, the function calls the function 
`File::ask_operating_system_for_file_descriptor`, which we will define below.

The statement `return File{file_descriptor: file_descriptor};`
then constructs a value of type `File` by specifying all its fields and
returns this.
Note that because the field `file_descriptor` is only accessible from within
the module, this way of constructing a value of type `File` is only available
within the `FileSystem` module.

```rust
		// Still in the `impl` block.
		fn ask_operating_system_for_file_descriptor(filepath: &FilePath) -> u64{
			unsafe {
				// the internals of a type that communicates with a resource
				// manager are typically unsafe.
				// We ommit it here.
			}
		}
		// Incomplete code, to be continued.
```

Usually, you don't have to communicate with a resource manager directly, you
use a function from the standard library to do the communication for you.
It's usually interfacing with an external API, I have left it out because it's
not the focus of this document.

Note that the function `ask_operating_system_for_file_descriptor` is not marked
`pub`, so it can only be called from within the module. 
In the function `open`, we already saw how it can be called.
Note the syntaxis `File::ask_operating_system_for_file_descriptor`. 
Because the function `ask_operating_system_for_file_descriptor` is defined in
the `impl` block of the type `File`, you have to specify the type, even when the function
does not have a parameter of type `File` or return a value of type `File`.
The function could also be defined as a stand-alone function outside of the
`impl` block, this would not make a difference.

```rust
		// Still in the `impl` block.
		pub fn read_byte(&self, index: usize) -> u8 {
			unsafe {
				// Yep, more unsafe stuff, which I omit.
			}
		}
```

The `&self` parameter is special.
It means that the first parameter, with name `self` is of type `&File` (because
we are in the `impl File` block).
Type `&File` means an immutable reference to a value of type `File`.
This implies that any borrower that borrows a value of type `File` as immutable
has access to read bytes from the file.
It also allows a special syntaxis: if `file` is a variable of type `File` or of type
`&File` or of type `&mut File`, then you can write `file.read_byte(3)` to
read the third byte from the file.

To simplify the matter, we assume that we will simply crash the current thread
when we have no access to read from the file, when we try to read past the
end of the file etc.
Of course, the real Rust API handles errors in a better way.

Because the field `file_descriptor` is not accessible from outside the 
`FileSystem` module, this is the only way a user of the code can read bytes
from the file.

```rust
		// Still in the `impl` block.
		pub fn file_size_in_bytes(&self) -> usize {
			unsafe {
				// Also ommitted.
			}
		}
		// Incomplete code, to be continued.
```

This function is similar to the `read_byte` function.
It also takes an `&self` parameter, which allows it to be used by any value
that borrows the value of type `File` as immutable.

```rust
		// Still in the `impl block.
		pub fn write_byte(&mut self, index: usize, byte_to_write: u8) {
			unsafe {
				// Ommitted.
			}
		}
	} // Close the impl block. Incomplete code, to be continued.
```

This function takes an `&mut self` parameter. 
It indicates that this function is restricted to references as mutable only.
Refferences as immutable cannot be passed to this function (because they have
a different data type).
In this way, access to write a byte to a file is restricted to references as mutable.
So you can only write to a file if you borrow it as mutable, not as immutable.

It is very important to not use a resource after it has been released.
Rust automatically inserts calls to release a resource.
This is done by calling the `drop` function from the `Drop` trait.
Let's see how the file `File` ensures that the file is closed properly.

```rust
	// Still in the `FileSystem` module
	impl Drop for File {
		fn drop(&mut self) {
			unsafe {
				// Again, this is unsafe code.
			}
		}
	}
} // End of the `FileSystem` module.
```

Here we see the syntaxis `impl Drop for File` is used to indicate that the
`File` struct implements the `Drop` trait.
The implementation can be found in the block immediately below.
The `Drop` trait defines only one function, called `drop` that takes one
`&mut self` parameter.
The implementation is again typically unsafe code that does communication
with an external API offered by the operating system.
What happens here is that the operating system is instructed to close the
file with the `file_descriptor` that was required during the initialisation.

Let us now see how the `File` struct can be used in practice.
We assume that the file struct is used outside of the `FileSystem` module.
Let us write a completely inappropriate method to copy data from one file to
another.
Let us first write the scaffolding of opening the files, calling another
function to copy the data, and closing the files.

```rust
// Outside the FileSystem module.

fn copy_file(source_filepath: &FilePath, destination_filepath: &FilePath) {
	let source_file = File::open(source_filepath);
	let mut destination_file = File::open(destination_filepath);
	copy_data(&source_file, &mut destination_file);
}

// To be continued...
```

On the first line of the function, we open the source file.
The value is immutable by default.
Note the `File::open(...)` syntaxis for calling a method associated to a type.
On the second line, we open the destination file.
This value is defined as mutable.
The third line is very interesting.
Here we call the `copy_data` function (that is still to be written).
This function borrows the `source_file` as immutable and it borrows the
`destination_file` value as mutable (that's what the `mut` after the `&` indicates).
Note that we do not write `drop(...)`, the compiler automatically inserts this
call for us.

Maybe it's time to let this sink a little, because this illustrates the foundation of
Rusts resource management.

Ok, on to the function that copies data.
As we already noted, this function borrows `source_file` as immutable and
`destination_file` as mutable.

```rust
// Still outside the FileSystem module.

fn copy_data(source: &File, destination: &mut File) {
	let length = source.file_size_in_bytes();
	for index in 0 .. length {
		let byte = source.read_byte(index);
		destination.write_byte(index, byte);
	}
}
```

The signature clarifies that `source` is borrowed as immutable from somewhere
and `destination` is borrowed as mutable.
When calling the `file_size_in_bytes` function, we use a syntaxis we didn't use
before.
An equivalent, but not as beautifull syntaxis would be
`let length = File::file_size_in_bytes(source);`.
Instead of passing the `&self` parameter explicitely, 
we use the dot-operator to call the function as is common in object-oriented languages.

Next comes a for-loop, where we loop over the indexes of all bytes in the source
file.

In the for-loop, we first read a byte from the `source`.
Again, note the syntaxis for this.
Then we write it to the destination.

Then we write the byte we have just read to the `destination` file.
Remember that the `File::write_byte` expects an `&mut self` parameter.
Here, `destination` is passed implicitely as the `&mut self` parameter, so it
must have type `&mut File`. This is why the function `copy_data` expects the
parameter `destination` to be of type `&mut File`.
