Memory and data structures
--------------------------

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
For this reason, Python and JavaScript do not know the concept of the stack and
the heap.
In Java and C#, the concept of the stack and the heap exist, but it does not
need to be the same as the stack and the heap that are offered by the hardware.
In Java for instance, the "Java Virtual Machine stack" may in fact be allocated
on the heap.

In "scripting" languages like Python and JavaScript, 
the program that actually runs is not the program that that is written in Python
or JavaScript.
Instead, the application that actually runs on the hardware is in fact
the Python interpreter if you use Python, or the browser or NodeJs if you use
JavaScript.
This acts as an extra layer between the software you want to run and the
hardware.
Functions written in Python or JavaScript cannot interact directly with the
dynamic memory allocation library or even the garbage collector.

Rust is a compiled language and the concepts of stack and heap matter.

For instance, if you run software written in Python or JavaScript, the program
that is actually executed is the Python interpreter or the JavaScript virtual
machine. 
These are programs (often written in C or C++) that read the code you wrote and
execute the instructions contained therein.

If you run software written in Java or C#, you 

Most gargbage-collected, object-oriented programming languages use the heap
very often.
Rust is different in this regard, and software written in Rust often relies more
on the stack.
Let us illustrate this by comparing a simple program in Java with an equivalent
in Rust and then comparing the two.
The situation will be the same for C#, D and 
Don't worry if you don't know Java, it is really a simple program:

```java
public class Person {
	int age;
	Person(int newAge) {
		this.age = new_age;
	}
}

public class Demo {
	public static void main(String[] args) {
		int i = 5;
		Person person = new Person(30).
	}
}
```

What is going on in the `main` function?
Recall that local variables of a function and arguments passed to a function
are stored on the stack.
Let us ignore the arguments (in this case `args`) and focus on the local
variables.
There are two local variables: `i` of data type `int` and `person` of data
type `Person`.
Both are stored on the stack.



TODO: write this section.
Explain that in Rust,
containers like `Box`, `Vec`, `HashMap` etc. store whatever they
contain on the heap, but that a plain `struct`, `enum`, tuple, array, closure is
on the stack (unless it is contained in a container).

This contrasts with many languages, where an object that instantiates a class
always lives on the heap.

TODO: explain what a 'value' is.
