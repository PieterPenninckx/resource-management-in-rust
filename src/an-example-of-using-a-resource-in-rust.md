An example of using a resource in Rust
--------------------------------------

Let us give an example illustrating how you can write to a file in Rust,
similar to the example in pseudo-code we gave at the very beginning.

For ease of reference, we repeat the introduction example here:

```
WRITE_TO_FILE()
1   file_descriptor ← OPEN_FILE("MyDocument.txt")
2   text_to_write ← "Hello, World!"
3   WRITE_TEXT(file_descriptor, text_to_write)
4   CLOSE_FILE(file_descriptor)
```

In the pseudo-code example we ignored the error handling.
In real Rust code, we cannot ignore errors.
For this reason, the equivalent in Rust is a bit longer.

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

In the first line of the function body, the program asks the operating system
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
