# #187 - Is this comment correct? [service vs owning context lifetime] [Closed]

> Username: traceon  
> Created at: 2019-01-05 16:42:45 UTC  
> Updated at: 2020-12-30 00:58:49 UTC  
> Closed at: 2020-12-30 00:58:48 UTC  
> Url: https://github.com/boostorg/asio/issues/187  

https://github.com/boostorg/asio/blob/fbe86d86b1ac53e40444e5af03ca4a6c74c33bda/include/boost/asio/execution_context.hpp#L72-L74  
  
The paragraph above doesn't seem correct, since the `execution_context::destroy()` is a protected member function, meaning any custom execution context that derives from `execution_contex` will be able to call it outside of its d-tor, or I am missing something?

---

## Comment 1

> Username: djarek  
> Created at: 2019-01-06 15:01:16 UTC  
> Url: https://github.com/boostorg/asio/issues/187#issuecomment-451748125  

It has to be a protected member so that the derived class can call `destroy()` in its own destructor. This is necessary, e.g. when you install a service that refers back to the derived context object and has to access its members in `shutdown()` or its destructor.   
  
In general, just because you can call it in any context, does not mean you should :).

---

## Comment 2

> Username: traceon  
> Created at: 2019-01-07 13:02:47 UTC  
> Url: https://github.com/boostorg/asio/issues/187#issuecomment-451927839  

I see, so together with this, that makes sense and is not conflicting:  
https://github.com/boostorg/asio/blob/fbe86d86b1ac53e40444e5af03ca4a6c74c33bda/include/boost/asio/execution_context.hpp#L86-L88  
  
but, are objects of derived classes allowed to call `destroy()` or `shutdown()` **before** they are destructed (to sort of erase the set of the services, for example)?

---

## Comment 3

> Username: djarek  
> Created at: 2019-01-07 21:49:52 UTC  
> Url: https://github.com/boostorg/asio/issues/187#issuecomment-452094216  

I believe they are allowed to do that, but the ExecutionContext might not be in a usable state (e.g. adding a service might be UB at that point), so definitely don't try reseting a context with them (unless there's a guarantee of that not being UB somewhere).

---

## Comment 4

> Username: ghost  
> Created at: 2020-12-30 00:58:47 UTC  
> Url: https://github.com/boostorg/asio/issues/187#issuecomment-752290962  

This issue was moved by [chriskohlhoff](https://github.com/chriskohlhoff) to [chriskohlhoff/asio#663](https://github.com/chriskohlhoff/asio/issues/663).
