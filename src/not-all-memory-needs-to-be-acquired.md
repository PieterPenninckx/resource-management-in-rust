Not all memory needs to be acquired
-----------------------------------

Each application gets at least two dedicated pieces of memory
that can be used without acquiring or releasing it (apart from the CPU
registers which you typically only encounter when you program in assembly).

First there is a piece of memory that can be used by the application for data
that has a fixed size and lives essentially as long as the application runs.
It is typically used for constants (e.g. if you want to store the number π, 
or string constants). This is called _static memory_.
You can do complicated stuff with static memory, but for simplicity, let us
assume that you will only want to store constants in static memory.

Secondly, there is a piece of memory that is used to store the local variables
of the functions and the arguments that are passed to the functions 
(and some other stuff as well). 
This is called the _stack_.
There is one stack per thread.
If you don't know what a thread is, don't worry about that for now, just 
know that each application uses at least one thread when it runs.
When a function call is made, a piece of the stack is used to store the local
variables of the function and the parameters to that function and when the function
is finished, this piece of memory is released.

This means that after a function call is finished, the memory that was used to
store the local variables of the function or the arguments that were passed to
that function, can be re-used for something completely else.
So you cannot expect the values that were stored in that piece of memory are
still intact. This implies that you cannot use the stack to store data that you
need for a longer time, longer than the duration of the function call. 

In some situations however, static memory and the stack are not enough. 
When you don't know how much memory you will need at compile time, you can't use
static memory (in general). 
And when you need to store memory longer than the duration of the function call
(without returning it from the function), you can't store it on the stack.
If you want to use some memory for a longer time and you can only know at run
time how much memory you will need, you can acquire a piece of memory that is
not in static memory and not on the stack.
We say that this is "on the heap".

This is just the same as with any other kind of resource we discussed before!
Memory on the heap has to be acquired explicitly and must be released after use,
either as a consequence of an explicit instruction in the program, or because
the garbage collector takes care of that.
For memory, a dynamic memory management library has the role of
resource manager and it distributes handles (memory addresses) that can be 
used by the processes.
The process can ask the dynamic memory management library for an arbitrary
amount of bytes and (if there is still memory available), the dynamic memory
management library returns the memory address of the beginning of a contiguous
region of memory that is not yet being used. The process can then use this
region of memory at will, but must instruct the dynamic memory library to
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

