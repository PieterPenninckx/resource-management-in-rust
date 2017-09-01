Resources in general
====================

We start with a simple specific example in pseudo-code.
Then we give some general theory and we conclude with a more extended example
in Rust.

A simple example
----------------

Consider the following pseudo-code.

```
WRITE_TO_FILE()
1   file_descriptor ← OPEN_FILE("MyDocument.txt")
2   text_to_write ← "Hello, World!"
3   WRITE_TEXT(file_descriptor, text_to_write)
4   CLOSE_FILE(file_descriptor)
```

On line 1, we call the function `OPEN_FILE` with as parameter the
name of the file we want to open.
This function `OPEN_FILE`, wich we do not give pseudo-code for, 
communicates to the operating system and asks to open the file with the given
file name.
For simplicity, we omit error handling in this example.

In general, an application can open more than one file.
So if the application asks the operating system to write to a file, the 
operating system has to know: "which file are you referring to?".
For this reason, the function `OPEN_FILE` returns an identifier
that will be used later on in the communication with the operating system to
specify which file the application is talking about.
In the case of a file, this identifier is called a _file descriptor_.
It is usually a number (an integer).
In most libraries, the file descriptor is wrapped in another data
type that represents the file.
In our example, we just assume that we get the bare file descriptor.

Line 2 is nothing special: we just store the text we want to write in a variable.

On line 3, we call the function `WRITE_TEXT` to write this text to the file.
The function `WRITE_TEXT` asks the operating system to write the given text
to a file.
The first parameter, for which we pass the variable `file_descriptor` is used
to specify to which file the text should be written.

On line 4, we call the function `CLOSE_FILE` to close the file.
Again, we pass the variable `file_descriptor` to specify which file must be
closed.

Excercise. In the previous example, the programmer can make a bug for instance
by forgetting to close the file. There are many mistakes like that.
Write down a number of them. We will come back to this later on.

Theory
------

In the introduction example, we have seen a simple program that communicates
to the operating system to write some text to a file.

The file is a specific example of the more broad concept of a _resource_.
Other examples of resources include 

* a network socket
* a connection to a server
* a block of memory
* access to the screen, soundcard, printer or any other device
* a mutual exclusive lock.

Some resources correspond to physical devices, other correspond to virtual
concepts (such as the mutual exclusive lock).

Resources are managed by a resource manager, usually the operation system.
The resource manager distributes handles that give access to this resource.
In the introduction example for instance, the handle was a file descriptor.

When a process wants to access a resource, it asks the resource manager for a
handle that gives access to the resource.
In the introduction example, the function `OPEN_FILE` asked the operating system
(the resource manager) for a file descriptor (the handle) that gives acces to
the file (the resource).
The resource manager may refuse access, or open the resource on behalf
of the process and hand over the handle to the process.
If the resource was opened successfully, the application stores the handle in
memory, to be used later on in the communication with the resource manager.

When the process has finished using the resource, it communicates to the resource
manager that the resource can be released: "Hey, I don't need this resource anymore".
In the introduction example, this was the call to the `CLOSE_FILE` function.
After that point, the process should not use the handle anymore.
When the resource manager is informed that a resource is not needed anymore, 
it can either close the resource, or it knows that
the resource can be re-used by another process that wants to use the resource again.

In general, using a resource looks as follows:

```
USE_RESOURCE()
1   handle ← OPEN_RESOURCE(parameters)
2   USE_RESOURCE(handle)
3   RELEASE_RESOURCE(handle)
```

Memory as a resource
====================

Memory is a resource just like any other, but in all programming
languages that I know, it is handled differently. 

One, quite obvious, difference is that in many programming languages, memory
does not have to be released explicitly because a garbage collector takes care
of that. Although Rust does not rely heavily on garbage collectors, I will spend
some time explaining garbage collectors.

A second difference is more subtle and we will discuss it later on.

But first, let us start with some examples of how memory is used in programming
languages you are probably more familiar with.

Introduction example
--------------------

Let us give one example in different programming languages: creating an
array or a vector or something similar of numbers, and storing the sum of
the numbers in the first element of the vector.

This is how this looks like in Python:

```python
def compute_sum():
	numbers = [1, 2, 3]
	numbers[0] = numbers[0] + numbers[1] + numbers[2]
```

In Java or C#:

```java
void ComputeSum() {
	int[] numbers = {1, 2, 3};
	numbers[0] = numbers[0] + numbers[1] + numbers[2];
}
```

In Javascript:

```javascript
function ComputeSum() {
	var numbers = [1, 2, 3];
	numbers[0] = numbers[0] + numbers[1] + numbers[2];
}
```

If you more familiar with a programming language for which no example was
given, you can probably write down this simple function yourself.

On the first line in the function body, an array is created, containing the 
numbers one, two and three. 
When any of the examples above is is executed, under the hood, a part
of memory is reserved to store the numbers. The variable `numbers` contains
somehow a handle for the piece of memory that was reserved.

On the second line, the handle stored in `numbers` is used multiple times to
compute a simple sum and store it in the first element of the array.

If the general approach to using a resource was used, then there would be a
third line on which the resource manager was informed that we don't need the
memory for the array anymore. There is no line of code like that.

If memory was like any other resource in these programming languages, then you
would get in trouble when this function was called many, many times. Each and
every time, the function would reserve yet another piece of memory to store
its array. After a while, there would be no unused memory left and your
program would crash. In practice, there is a piece of your application that
prevents this from happening: the garbage collector.

Garbage collectors
------------------

In the previous section, we illustrated that in many programming
languages, the user does not have to explicitely indicate that a piece of
memory is no longer needed. But if the application were to only reserve more
and more memory, it would soon run out of memory. So ultimately, somehow the
application has to indicate that some pieces of memory are not used anymore and
may be re-used for something else. 

This is the task of the garbage collector. 
The garbage collector is a piece of software designed only for this goal
and it runs together with your application.
In order to know which pieces of
memory may be released, the garbage collector analyses the state of the program
as it runs. 

There are many techniques for garbage collecting.
One technique, called 'reference counting' can be implemented relatively easy
and requires only a little help from the programming language.
Other techniques require extensive support from the programming languages.

Most recent programming languages, including Python, Javascript, Java, C# and Go
offer extensive support for garbage collecting. In these languages, you 
typically rely on the garbage collector for nearly everything you do.

C++, Rust and Swift have enough features to implement reference counting, 
but do not offer any support for garbage collection beyond that. 
For this reason, it is often said that these languages do not have garbage
collection, although technically speaking, that statement is not correct.

Reference counting is only one technique to manage memory in Rust, however, and
it is even not used that often. Depending on the type of software you are
writing, you can write a complete program and then realize that you never used
reference counting.

Not all memory needs to be acquired
-----------------------------------

Memory as a resource is a special case, because the handle that gives access
to the resource is stored in memory as well.
So if you want to use memory, then you need to get a handle that gives access to the memory.
You then need to store this handle in memory, and for this, you have to acquire memory.
Eum... this is a "the chicken or the egg" dilemma!

This dilemma is solved by giving the application a dedicated piece of
memory that can be used without needing to acquire or release it.
In fact, each application gets at least two dedicated pieces of memory without
needing to acquire or release it.

First there is a piece of memory that can be used by the application for data
that has a fixed size and lives essentially as long as the application runs.
It is typically used for constants (e.g. if you want to store the number π, 
or string constants). This is called _static memory_.
You can do complicated stuff with static memory, but for simplicity, let us
assume that you will only want to store constants in static memory.

Secondly, there is a piece of memory that is used to store the local variables
of the functions and the parameters that are passed to the functions 
(and some other stuff as well). 
This is called the _stack_.
There is one stack per thread.
If you don't know what a thread is, don't worry for now about that, just 
know that each application uses at least one thread when it runs.
When a function call is made, a piece of the stack is used to store the local
variables of the function and the parameters to that function and when the function
is finished, the memory is released.

This means that after a function call is finished, the memory that was used to
store the local variables of the function or the parameters that ware passed to
that function, can be re-used for something completely else.
So you cannot expect the values that were stored in that piece of memory are
still intact. This implies that you cannot use the stack to store data that you
need for a longer time, longer than the duration of the function call. 

These two pieces of memory -- static memory and the stack -- solve the
chicken-and-egg dilemma we mentioned earlier.
In some situations however, this is not enough. 
When you don't know how much memory you will need at compile time, you can't use
static memory. 
And when you need to store memory for a longer time, you can't store it on
the stack.
If you want to use some memory for a longer time and you can only know at run
time how much memory you will need, you can acquire a piece of memory that is
not in static memory and not on the stack.
We say that this is "on the heap".

This is just the same as with any other kind of resource we discussed before!
Also for memory, the operating system has the role of resource manager, and it
distributes handles (memory adresses) that can be used by the processes.
But this is very low level stuff and it also has a large overhead because 
you need a system call to the operation system, which is relatively expensive.
So there is a dynamic memory management library that plays an intermediate role
between the application and the operating system. It asks the operation system
for pieces of memory in larger chunks (typically 4kB or more) and distributes
smaller pieces of this to the application. In this way, it avoids having to do
a system call for each and every small piece of memory that the application
needs.

This memory management library plays the role of a resource manager.
You can ask it to give an amount of memory and it negociates with the operating
system for you.
From the point of view of the application, it's still a resource manager where
you have to acquire a piece of memory, or release a piece of memory.

Memory that is obtained in this way is memory _on the heap_.
Acquiring memory access is called _memory allocation_ and releasing memory is called _freeing_.

Note that a garbage collector is not the same as a dynamic memory management 
library.
A dynamic memory management library requires you to tell explicitly
when you don't need a specific part of memory anymore.
