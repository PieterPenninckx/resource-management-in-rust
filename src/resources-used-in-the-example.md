Resources used in the example
-----------------------------

Apart from the memory that the application needs to store its CPU-instructions,
it also needs the following resources when executing the function 
`write_to_file` described above:

* A piece of memory to store the the variable named `file`.
  Because this is a local variable, it is stored on the stack.
* The file name to be passed to the function `File::create`. Because this is a
  constant, it is stored in static memory.
* The file name needs to be passed to the function `File::create`. In fact, the
  function `File::create` does not expect the file name itself as a parameter,
  but only the memory address of the file name and the length.
  Because the file name is stored in static memory, its memory address is in 
  fact a constant (TODO: this needs to be fact checked) and this constant can 
  be stored as part of the CPU-instructions of the application.
  Also the length of the file-name is a constant.
* When the function `File::create` is called, the function `File::create` also
  needs some resources. For instance, it stores its parameters (in this
  case the memory address and the length of the filename) on the stack while
  it executes.
* Of course, the open file itself is also a resource used by the
  application. A handle to this file (a file descriptor) is stored in
  the variable `file`.
* The text that will be written to the file. This is again a constant, so it
  is stored in static memory.
* The function `File::write_all` expects the memory address of a 
  variable of type `File`. This memory address is notated as `&mut file` 
  in the example. In fact, `&` means "take the memory address of".
  The `mut` indicates that the function `File::write_all` has exclusive
  access to this memory address while it is executed. This will be explained
  in further detail when we talk about "borrowing as mutable".
* The function `File::write_all` also expectsthe memory address and the length
  of the data to be written. This is similar to the filename parameter of the
  function `File::create`.
* The caller of `write_to_file` will store the return value of
  `write_to_file`.
* For completeness: when the function returns, it needs to know where it has to
  return to. This information is also stored on the stack.
