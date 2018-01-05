Not all memory needs to be acquired
-----------------------------------

Each application gets at least two dedicated pieces of memory
that can be used without acquiring or releasing it.

### Static memory

First there is a piece of memory that can be used by the application for data
that has a fixed size and lives essentially as long as the application runs.
It is typically used for constants (e.g. if you want to store the number π, 
or string constants). This memory is in the data segment of
the application and it is called _static memory_.
You can do complicated stuff with static memory, but for simplicity, let us
assume that you will only want to store constants in static memory.

### The stack

Secondly, there is a piece of memory that is used to store the local variables
of the functions and the arguments that are passed to the functions 
(and some other stuff as well). 
This is called the _stack_.

There is one stack per thread and it typically has a fixed size.
If you don't know what a thread is, don't worry about that for now, just 
know that each application uses at least one thread when it runs.

When a function `a()` calls a function `b()`, we say that `a` is the _caller_
and `b` is the _callee_.
When a function call is made, a piece of the stack is reserved to store the
local variables of the callee, the parameters to that callee and some other
stuff that is needed to make it all work.
This piece of the stack is called the _stack frame_ corresponding to the
callee.
When the callee returns, the stack frame corresponding to the callee is released
and  may be re-used for something else.

This means that you cannot expect the values that were stored in stack frame
of the callee are still intact when the callee has returned.
This implies that you cannot use the stack to store data that you
will still need after the callee has returned.

How exactly the return value of a function is transferred from the callee to
the caller depends on the CPU architecture and on the number of bytes that need
to be returned.
In order to understand this document, you may think that the caller treats the
return value of the callee as a local variable of the caller and stores it on
the stack frame of the caller.
The callee gets an extra parameter that indicates where the return value should
be stored.


### The heap

In some situations, static memory and the stack are not enough. 
When you don't know how much memory you will need at compile time, you can't use
static memory (in general). 
And when you need to store memory longer than the duration of the function call
(without returning it from the function), you can't store it on the stack.
If you want to use some memory for a longer time and you can only know at run
time how much memory you will need, you can acquire a piece of memory that is
not in static memory and not on the stack.
We say that this is "on the heap".

Memory on the heap has to be acquired explicitly and must be released after use,
either as a consequence of an explicit instruction in the program, or because
the garbage collector takes care of that.
This is just the same as with any other kind of resource we discussed before!
For memory, a dynamic memory management library has the role of
resource manager and it distributes handles (memory addresses) that can be 
used by the processes.
The process can ask the dynamic memory management library for an arbitrary
amount of bytes and (if there is still memory available), the dynamic memory
management library returns the memory address of the beginning of a contiguous
region of memory that is not yet in use. The process can then use this
region of memory and must instruct the dynamic memory library to
release the memory after use.

The dynamic memory management library plays an intermediate role
between the application and the operating system. In this way, it avoids
having to do a system call -- which is rather 'expensive' -- for each and
every small piece of memory that the application needs.

The garbage collector works on top of the dynamic memory management library.
The garbage collector analyses both the stack and the heap to determine which
memory on the heap is not needed anymore. 
When it finds unneeded memory on the heap, it asks the dynamic memory management
library to release the unneeded memory.

Acquiring memory access is called _memory allocation_ and releasing memory is called _freeing_.

If you haven't heard about the distinction between the stack and the heap yet,
that's probably because in many languages this distinction does not exist or is
not so important.

The concepts of the stack and the heap are important when your software runs
immediately on the hardware.
In interpreted or JIT-compiled languages like Python, Javascript, Java and C#,
the software doesn't run immediately on the hardware, but it is rather executed
by the Python interpreter, Node.js, the browser, the JVM or the CLR.
The Python interpreter, Node.js, the browser, the JVM or the CLR use the stack
and the heap for their own functions, but may hide some aspects of memory
allocation for the programmer.
For this reason, in Python and JavaScript the concepts of the stack and
the heap are not so important.
In Java and C#, the concept of the stack and the heap exist, but it does not
need to be the same as the stack and the heap that are offered by the hardware.
In Java for instance, the "Java Virtual Machine stack" may in fact be allocated
on the heap.

If you compile Rust to a native executable, the resulting application uses the
stack and the heap as described above.
If you compile Rust code to WebAssembly however, it no longer runs directly on
the hardware, but in an "execution environment" such as a web browser.
This means that, even while the _result_ of executing the code may be the same
as when it would run on the hardware directly, memory management is done
differently. 
Nevertheless, even in this context, the concepts of the stack and the heap
are still relevant because they determine how long a piece of memory is kept
intact and if the program has to do some extra work to indicate that a piece
of memory may be re-used.

### The stack and the heap in action

Let us give an example of all three types of memory (stack, heap and static
memory) in the C programming language.
This is not a course in the C programming language, so I keep the example very
simple and I also do some things that you shouldn't really do in C.

```C
do_something() {
	int integer_number_on_the_stack = 10000;
}

int return_something() {
	return 500;
}

main() {
	void* memory_address_of_something_in_static_memory = "abc";
	
	int data_on_the_stack = 10000;
	void* memory_address_of_something_on_the_stack = &data_on_the_stack;
	do_something();
	int other_data_on_the_stack = return_something();
	
	void* memory_address_of_something_on_the_heap = malloc(100);
	free(memory_address_of_something_on_the_heap);
}
```

In the `main` function, the variable 
`memory_address_of_something_in_static_memory` holds the memory address of the
first byte of a string constant.
The data type "`void *`" means "memory address of something".

TODO: continue on this.
