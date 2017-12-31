Memory and data structures
--------------------------

An illustration of the difference between value types and reference types
=========================================================================

I will give some examples in C# in this section, but you should be able to
understand what is going on if even if you don't know C#.

Consider the following piece of code:

```C#
void ValueType() {
	int i = 1;
	int j = i;
	j = 2;
	Console.WriteLine(i);
}
```
The statement `int j = i` makes sure that a piece of memory is reserved to
hold the value of the variable `j`. The value of `i` is copied to this piece of
memory. When `j = 2` is executed, the value of `j` is changed.
Because `i` and `j` do not share any memory, the value of `i` is unchanged and
`Console.WriteLine(i)` will print "1".

This contrasts with the following piece of code, still in C#:

```C#
class MyClass {
	int MyField;
}

void ReferenceType() {
	MyClass a = new MyClass {
		MyField = 1
	};
	MyClass b = a;
	b.Myfield = 2;
	Console.WriteLine(a.MyField);
}
```

When this function is executed, the output will be "2" instead of "1".
Other languages, like Java, Python, JavaScript etc behave similarly.
This is confusing if you haven't seen this before, so I will explain in detail
what is going on.

The statement
```
MyClass a = new MyClass {
	MyField = 1
};
```
creates a new variable named `a` and assigns a new value of type `MyClass` to it.
This value of type `MyClass` does not contain the value `1` of the field 
`MyField`, but contains the memory address of _another_ piece of memory.
This second piece of memory is allocated to 
store the values of all the fields (in this case only
the field `MyField`) plus some data that is needed for polymorphism.
So `a` contains no actual data, but only the memory
address of the region in memory where the data is stored.
The assignment `MyClass b = a;` creates a new variable named `b` and stores the
same memory address that `a` already has in this new variable `b`.
So `a` and `b` point to the same region in memory.
The assignment `b.MyField = 2;` essentially says the following:

* Get a memory address out of the variable `b`. This memory address marks the
  first byte of a region in memory.
* Assign `2` to the value of the field `MyField` that can be found in that region.

The next line, `Console.WriteLine(a.X);`, then essentially says the following:

* Get a memory address out of the variable `a`. This memory address marks the
  first byte of a region in memory.
* Get the value of the field `MyField` that can be found in that region.
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
Primitive data types, like integers, booleans, floating point numbers etc.
are most often value types and classes are reference types.
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
struct MyStruct {
	my_field: i32
}

fn still_value_type() {
	let mut a : MyStruct = MyStruct {my_field : 1};
	let b : MyStruct = a;
	b.my_field = 2;
	println!("{}", a.my_field); // Prints "1"
}
```

When the function is called, the whole value of `a` is copied to `b`
(which is even explicit with the `#[derive(Clone, Copy)]` notation that we
will discuss further on (TODO: where?)) and 
hence `a` and `b` represent completely different entities.
When we change `b`, because it has nothing to do with `a` anymore, `a` is left
intact and "1" will be printed.

If we were to try to mimick the second C# example, we would have something like 
the following:

```rust
#[derive(Clone, Copy)]
struct MyStruct {
	my_field: i32
}

fn reference_type() {
	let a : &mut MyStruct = &mut MyStruct {my_field : 1};
	{
		let b : &mut MyStruct = a;
		b.my_field = 2;
	}
	println!("{}", a.my_field);
}
```

See the `&`s in the code? 
These indicate that there is a reference.
In Rust, references are nearly always explicit.

### Data structures and the stack and the heap in Rust

TODO: Explain that in Rust,
containers like `Box`, `Vec`, `HashMap` etc. store whatever they
contain on the heap, but that a plain `struct`, `enum`, tuple, array, closure is
on the stack (unless it is contained in a container).

TODO: explain what a 'value' is.
