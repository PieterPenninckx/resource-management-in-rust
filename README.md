Resource management in Rust
===========================

Documentation about resource management in the Rust programming language.
We zoom in on some concepts that are specific to Rust:

* ownership
* borrowing
* lifetimes

[Read online.](https://pieterpenninckx.github.io/resource-management-in-rust/)

Meta
----

This is a work in progress. It is not part of the official Rust documentation.

Target audience
---------------

This documentation is for you if you

* are learning the Rust programming language and you want to understand 
  lifetimes, ownership, borrowing etc. better,
* already understand the basics (structs, enums, traits, ...),
* have some experience with a garbage-collected object oriented programming
  language,
* are comfortable reading simple technical English,
* have no problem with the fact that this documentation 
  is probably a work in progress for ever.

Building
--------

We use [mdBook](https://github.com/azerupi/mdBook) to build the HTML version
of the documentation. You can install mdBook with the following command:

```
cargo install mdbook
```

To build, go to the folder containing the `src` folder and run the following
command:

```
mdbook build
```

The result is in the `book` directory.

Contributing
------------

We welcome contributions, big and small. See [CONTRIBUTING](CONTRIBUTING.md) for
details.

License
-------

"Resource management in Rust" is licensed under the Apache License, Version 2.0
(see [LICENSE-APACHE](LICENSE-APACHE)), 
or the MIT license (see [LICENSE-APACHE](LICENSE-APACHE)), at your opinion.
