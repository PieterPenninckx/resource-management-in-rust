Table of contents
=================

This table of contents serves mostly to write down how we want to structure
the document.

Resources in general
--------------------

* Simple example
* What is a resource?
* Memory as a resource
	- Example
	- Garbage collectors
	- Static memory, stack, heap
* Recap (TODO)

Resources in Rust (introduction)
--------------------------------

* Simple example
* Ownership: responsibility for closing a resource
* Borrowing: using a resource (TODO)
	- Borrowing as immutable: non-exclusive usage, read-only
	- Borrowing as mutable: exclusive usage, allowing writes
* Moving: transfer of ownership (TODO)
	- Move semantics: only one owner may be responsible
	- Copy semantics: not all data types correspond to owners of a resource
* Encapsulation prevents fiddling with the resource from outside (is now DRAFT)
	- Example
	- Handles are stored in private fields to prevents fiddling from outside
	- Asking for an `&mut` parameters when you offer something exclusive
* Recap

Ownership (TODO)
----------------

* Examples of owners
	- `String`
	- `Box<T>`
	- `Vec<T>`
	- ...
* Initialising a value is opening a resource
* Resources are released when the owner goes out of scope
	- When a value that is no owner anymore goes out of scope, no resource
		is released (that is the responsibility of the new owner now)
	- Named variables: at the end of lexical scope, in reversed order
	- Anonymous variables (temporaries): at the end of the statement
		- Temporaries in the condition of an `if` or a `while`
		- Exception
	- Resources in fields are released after the containing struct is released
	- Drop order of fields
	- `mem::drop()`
* `Rc<T>` and `Arc<T>` allow to decide at run-time when a resource must be
  released
* Resources leak when they are not released
	* `mem::forget`
	* cyclic references with `Rc<T>` and `Arc<T>` cause leaks
* Recap

Borrowing (TODO)
----------------

* Examples of borrowing
	* functions with an `&` or an `&mut` parameter
	* references
	* structs and enums etc. may borrow when they contain a reference
	* closures may borrow
* Slices
* `Box<T>` is an owner of `T` and `&T` and `&mut T` are borrowers
* `String` is an owner and `&str` is a borrower
* Interaction with ownership 
	- you may only use a resource that has been opened
	- you must finish using a resource before it is released
		- Returning a `Box<T>` instead of an `&T`
* Partially borrowing
	* of a struct
	* of a slice: `split_at()` and `split_at_mut()`
	* compound data-types make partially borrowing easier
* Interior mutability: checking access at run time instead of compile-time
	* `Cell`
	* `Refcell`
	* `Mutex`
	* `RwLock`
* Recap

Moving (TODO)
-------------

* Examples of moving
	- returning from a function
	- result of an expression
	- passing a parameter to a function
	- moving closure
* Copy semantics: not all data types correspond to owners of a resource
* Interaction with ownership: the old owner does not need to release the resource
  anymore
* Interaction with borrowing: cannot move out of borrowed content
	- `mem::swap()` and `Option::take()`
* Recap

Lifetime annotation
-------------------
TODO
