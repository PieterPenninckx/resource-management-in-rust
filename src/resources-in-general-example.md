A simple example
----------------

For simplicity, we omit error handling in this example.
Consider the following pseudo-code.

```
WRITE_TO_FILE()
1   file_descriptor ← OPEN_FILE("MyDocument.txt")
2   text_to_write ← "Hello, World!"
3   WRITE_TEXT(file_descriptor, text_to_write)
4   CLOSE_FILE(file_descriptor)
```

On line 1, we call the function `OPEN_FILE` and we pass as parameter the
name of the file we want to open.
This function `OPEN_FILE`, wich we do not give pseudo-code for, 
communicates with the operating system and asks to open the file with the given
file name.

In general, an application can open more than one file.
So if the application asks the operating system to write to a file, the 
operating system has to know: "Which file are you referring to?".
For this reason, the function `OPEN_FILE` returns a number
that will be used later on in the communication with the operating system to
specify which file the application is talking about.
In the case of a file, the number is called a _file descriptor_.
In most libraries, the file descriptor is wrapped in another data
type that represents the file.
In our example, we just assume that we get the bare file descriptor.

Line 2 is nothing special: we just store the text we want to write in a variable.

On line 3, we call the function `WRITE_TEXT` to write this text to the file.
The function `WRITE_TEXT` asks the operating system to write the given text
to a file.
The first parameter, for which we pass the variable `file_descriptor`
specifies to which file the text should be written.

On line 4, we call the function `CLOSE_FILE` to close the file.
Again, we pass the variable `file_descriptor` to specify which file must be
closed.

Excercise. In the previous example, the programmer can make a bug for instance
by forgetting to close the file. There are many mistakes like that.
Write down a number of them. We will come back to this later on.
