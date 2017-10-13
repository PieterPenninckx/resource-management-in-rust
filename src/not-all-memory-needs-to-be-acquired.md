Not all memory needs to be acquired
-----------------------------------

Memory as a resource is a special case, because the handle that gives access
to the resource is stored in memory as well.
So if you want to use memory, then you need to get a handle that gives access to the memory.
You then need to store this handle in memory, and for this, you have to acquire memory.
Eum... this is a "the chicken or the egg" dilemma!

This dilemma is solved by giving each application a dedicated piece of
memory that can be used without acquiring or releasing it.
In fact, each application gets at least two dedicated pieces of memory.

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
If you don't know what a thread is, don't worry about that for now, just 
know that each application uses at least one thread when it runs.
When a function call is made, a piece of the stack is used to store the local
variables of the function and the parameters to that function and when the function
is finished, this piece of memory is released.

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
static memory (in general). 
And when you need to store memory for a longer time, you can't store it on
the stack.
If you want to use some memory for a longer time and you can only know at run
time how much memory you will need, you can acquire a piece of memory that is
not in static memory and not on the stack.
We say that this is "on the heap".

This is just the same as with any other kind of resource we discussed before!
Memory on the heap has to be acquired explicitely and must be resude after use,
either as a consequence of an explicit instruction in the program, or because
the garbage collector takes care of that.
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
you have to acquire a piece of memory and release it after use.

Note that a garbage collector is not the same as a dynamic memory management 
library.
A dynamic memory management library requires you to tell explicitly
when you don't need a specific part of memory anymore.

Acquiring memory access is called _memory allocation_ and releasing memory is called _freeing_.
