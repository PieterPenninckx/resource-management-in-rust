What is a resource
------------------

In the introduction example, we have seen a simple program that
writes some text to a file.

The file is a specific example of the more broad concept of a _resource_.
Other examples of resources include 

* a network socket,
* a connection to a server,
* a block of memory,
* access to the screen, sound card, printer or any other device,
* a mutual exclusive lock.

Some resources correspond to physical devices, other correspond to virtual
concepts (such as the mutual exclusive lock).

Resources are managed by a resource manager, usually the operation system.
The resource manager distributes handles that give access to this resource.
In the introduction example for instance, the handle was a file descriptor.

When a process wants to access a resource, it asks the resource manager for a
handle that gives access to the resource.
In the introduction example, the function `OPEN_FILE` asked the operating system
(the resource manager) for a file descriptor (the handle) that gives access to
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
GENERAL_FLOW_OF_USING_A_RESOURCE()
1   handle ← OPEN_RESOURCE(parameters)
2   USE_RESOURCE(handle)
3   RELEASE_RESOURCE(handle)
```
