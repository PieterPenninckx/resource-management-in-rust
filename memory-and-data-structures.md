Memory and data structures
--------------------------

TODO: write this section.
Explain that in Rust,
containers like `Box`, `Vec`, `HashMap` etc. store whatever they
contain on the heap, but that a plain `struct`, `enum`, tuple, array, closure is
on the stack (unless it is contained in a container).

This contrasts with many languages, where an object that instantiates a class
always lives on the heap.

TODO: explain what a 'value' is.
