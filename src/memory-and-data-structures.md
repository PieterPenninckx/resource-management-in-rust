Memory and data structures
--------------------------

Values and references
=====================

I will give some examples in C# in this section, but you should be able to
understand what is going on if even if you don't know C#.

Consider the following piece of code:

```C#
void ValueType() {
	int i = 1;
	int j = i;
	i = 2;
	Console.WriteLine(j);
}
```
The statement `int j = i` makes sure that a new place in memory is reserved to
hold the value of the variable `b`. The value of `i` is copied to this new place
in memory. When we write `i = 2`, the value of `i` is changed.
Because `i` and `j` do not share any memory, the value of `j` is unchanged and
`Console.WriteLine(j)` will print "1".

This contrasts with the following piec of code, still in C#:

```C#
class A {
	int X;
}

void ReferenceType() {
	A a = new A{
		X = 1
	};
	A b = a;
	a.X = 2;
	Console.WriteLine(b.X);
}
```

When this function is executed, the output will be "2" instead of "1".
Similar behaviour can be observed in other languages, like Java, Python,
JavaScript etc.
This is confusing at first, so I will explain in detail what is going on.
To explain why we get a different output, let us step trough the example.

The statement
```
A a = new A {
	X = 1
};
```
creates a new variable named `a` and assigns a new value of type `A` to it.
The new value has its field `X` initialised to `1`.
But under the hood, a lot more is going on.
To store the value, a piece of memory is allocated to 
store the values of all the fields (in this case only
the field `X`) plus some data that is needed for polymorphism.
The memory address of this piece of memory is then stored in `a`.
Let me rephrase that: `a` only contains no actual data, but only the memory
address of the region in memory where the data is stored.
The assignment `A b = a;` creates a new variable named `b` and stores the
same memory address that `a` already has in this new variable `b`.
The assignment `a.X = 2;` essentially says the following:

* Get a memory address out of the variable `b`. This memory address marks the
  first byte of a region in memory.
* Assign `2` to the value of the field `X` that can be found in that region.

The next line, `Console.WriteLine(b.X);`, then essentially says the following:

* Get a memory address out of the variable `b`. This memory address marks the
  first byte of a region in memory.
* Get the value of the field `X` that can be found in that region.
* Print out that value.

Now because `a` and `b` hold the same memory address, they essentially still
represent the same object and "2" is printed out.

I think we can agree that there is a big difference between the first example
and the second.
The difference is that, in C#, `int` is a _value type_ and the class `A` is a 
_reference type_.
A value type is a data type for which the variables store the data directly.
A reference type is a data type that contains the memory address of the region
in memory where the data is stored.
Most programming languages have both value types and reference types.
Primitive data types, like integers, booleans, floating numbers, are most often
value types and classes are reference types.
In Java, `int` is a value type and `Integer` is a reference type (and similar
for some other primitive types).
Strings are tricky business in any language (at least in my opinion).

### Value types and reference types in Rust

Most languages need to distinguish value types from reference types because the
reference (taking the memory address or following a memory address) is implicit.
In Rust, references are (nearly) always explicit.
For this reason, one rarely talks about value types and reference types in Rust.

Let us "translate" the first example from C# to Rust:

```rust
fn value_type() {
	let i : i32 = 1;
	let j : i32 = i;
	i = 2;
	println!("{}", j);
}
```

This is very close to the C# example, in particular, the value of `i` is copied
to the value of `j`.

`struct`s in Rust are more similar to `struct`s in C# or D than to classes in
other languages. So the following example is only seemingly similar to the
second example in C# given above. In fact, the output is completely different.

```rust
#[derive(Clone, Copy)]
struct A {
	x: i32
}

fn still_value_type() {
	let mut a : A = A {x : 1};
	let b : A = a;
	a.x = 2;
	println!("{}", b.x);
}
```

There is not a single `&` to be found in this code snipplet and it is very
self-containing, so you can be pretty sure there is no reference anywhere in the
code you see.
And indeed, when the function is called, the whole value of `a` is copied to `b`
(which is even made obvious with the `#[derive(Clone, Copy)]` notation) and 
hence `a` and `b` represent completely different entities.

If we were to try to mimick the C# example, we would have something like the
following:

```rust
#[derive(Clone, Copy)]
struct A {
	x: i32
}

fn try_to_immitate_reference_type() {
	let a : &mut A = &mut A {x : 1};
	let b : &mut A = a;
	a.x = 2;
	println!("{}", b.x);
}
```

See the `&`s in the code? This indicates that there is a reference.
The problem with this code is that it does not even compile.
Why?
Let us look again at the second example in C#:
```
	// ...
	a.X = 2;                (1)
	Console.WriteLine(b.X); (2)
```
You need more context to know whether line (1) affects the output on line (2).
In the simple example, the context was just a few lines above, but in more
complicated code and especially with more complicated data structures, it can
become hard to know where exactly data is being modified.

### Data structures and the stack and the heap in Rust

TODO: Explain that in Rust,
containers like `Box`, `Vec`, `HashMap` etc. store whatever they
contain on the heap, but that a plain `struct`, `enum`, tuple, array, closure is
on the stack (unless it is contained in a container).

TODO: explain what a 'value' is.
