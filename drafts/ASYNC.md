# Python's Async


Python doesn't have a unified/comprehensive approach to asyncronity.

`node.js` provides an api that cover's most basic functions that need special care:
network operations, file operations, socket operations, etc. These things will cause
the entire thread to be suspended, and the entire task manager with it. So they have
to be done on a different thread. With two threads, there is still the GIL- these operations
will give up the GIL before the block, so that's no problem. (Node doesn't have a thing named a GIL
but that's an implementation detail- it is single threaded).

So coming from `node`, you might expect an API to use these basic functions that plays nice with the above complexity,
but you don't really have that.

You do, have, however, `to_thread` and `run_in_executor`, and I am not convinced that we should use any framework
giving us a nice API if we have to and can just use the blocking functions.
