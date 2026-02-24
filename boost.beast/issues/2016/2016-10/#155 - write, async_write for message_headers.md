# #155 - write, async_write for message_headers [Closed]

> Username: vinniefalco  
> Created at: 2016-10-25 22:45:31 UTC  
> Updated at: 2016-11-12 17:42:46 UTC  
> Closed at: 2016-11-12 17:42:46 UTC  
> Url: https://github.com/boostorg/beast/issues/155  

We could use overloads of `write` and `async_write` which accept a `message_headers` object.  
  
Consider refactoring existing write functions to first write the headers using these new routines.
