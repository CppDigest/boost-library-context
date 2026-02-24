# #365 - Return events from all command_queue::enqueue_* methods [Closed]

> Username: kylelutz  
> Created at: 2014-12-29 18:27:59 UTC  
> Updated at: 2017-04-10 19:27:22 UTC  
> Closed at: 2017-04-10 19:27:22 UTC  
> Url: https://github.com/boostorg/compute/issues/365  

Currently all of the asynchronous methods in the `command_queue` class return `event` objects. For consistency, we should also return events from the synchronous methods (like `enqueue_write_buffer()`). These events can be useful for chaining together other operations or performance profiling.  
##
