# #188 - Virtual execution_context::~execution_context ? [Closed]

> Username: traceon  
> Created at: 2019-01-05 17:53:49 UTC  
> Updated at: 2020-12-30 00:58:56 UTC  
> Closed at: 2020-12-30 00:58:54 UTC  
> Url: https://github.com/boostorg/asio/issues/188  

https://github.com/boostorg/asio/blob/84b95e1d8d8c6e669de2842b83ce06c820e32be7/include/boost/asio/impl/io_context.hpp#L330-L333  
  
This `static_cast` above won't cause undefined behavior due to type checks made in the `use_service()` overload for `io_context` (however, the default version of `make_service()` is still going to be used, right?):  
https://github.com/boostorg/asio/blob/84b95e1d8d8c6e669de2842b83ce06c820e32be7/include/boost/asio/impl/io_context.hpp#L33-L40  
  
But what is the recommended way of doing the same for custom `execution_context` derivatives, and their services, that need to access functionality that is beyond the base `execution_context` class? Safe downcasts are not possible, since the `execution_context` is not polymorphic. So maybe making its d-top virtual is justified, for such usage cases?  
https://github.com/boostorg/asio/blob/84b95e1d8d8c6e669de2842b83ce06c820e32be7/include/boost/asio/execution_context.hpp#L118

---

## Comment 1

> Username: djarek  
> Created at: 2019-01-06 14:55:42 UTC  
> Url: https://github.com/boostorg/asio/issues/188#issuecomment-451747742  

There is no need to make `execution_context` polymorphic - it's not supposed to be used that way. If you need to install services that rely on a particular ExecutionContext, you can do it safely by enforcing that it's only ever initialized using the particular ExecutionContext type you're interested in:  
https://godbolt.org/z/4JPiVK  
  
This is basically the same pattern that asio uses in `io_context`. Note that services should in general be context-agnostic - they should work with any ExecutionContext (sadly it's not always possible to do this).

---

## Comment 2

> Username: traceon  
> Created at: 2019-01-07 13:12:11 UTC  
> Updated at: 2019-01-07 13:12:35 UTC  
> Url: https://github.com/boostorg/asio/issues/188#issuecomment-451930138  

Right, but then `add_service<>()`, `make_service<>()`, `use_service<>()` cannot be used to access/create instances of such services, can they?  
  
> Note that services should in general be context-agnostic - they should work with any ExecutionContext (sadly it's not always possible to do this).  
  
I am trying to understand this concept and use it correctly, as intended, so could you please point me to a resource that describes this in detail? If services should be context-agnostic, what is the purpose of having different ExecutionContext's, if they are not going to provide any additional resource or feature to the associated Services?

---

## Comment 3

> Username: djarek  
> Created at: 2019-01-07 13:53:26 UTC  
> Url: https://github.com/boostorg/asio/issues/188#issuecomment-451941315  

> I am trying to understand this concept and use it correctly, as intended, so could you please point me to a resource that describes this in detail?  
  
I don't think such a resource exists.  
  
> If services should be context-agnostic, what is the purpose of having different ExecutionContext's, if they are not going to provide any additional resource or feature to the associated Services?  
  
The primary use for Services is to provide "global", lazily constructed storage that's shared between multiple instances of an I/O object. For example, a socket is nothing more than a handle to a state allocated inside a reactor, and the reactor is installed as a service in the context. The reactor itself never touches the ExecutionContext directly, only uses services stored by it. The context (and its executor) itself is there to enable customizing scheduling and execution semantics, e.g. you could have a multithreaded context that allows pinning certain operations, and their continuations to a particular thread (not possible with an `io_context` that has multiple running threads).

---

## Comment 4

> Username: traceon  
> Created at: 2019-01-09 11:37:07 UTC  
> Url: https://github.com/boostorg/asio/issues/188#issuecomment-452666528  

> e.g. you could have a multithreaded context that allows pinning certain operations, and their continuations to a particular thread (not possible with an io_context that has multiple running threads).  
  
This is exactly what I wanted to accomplish, and I need to expose internally maintained `io_context` to Services somehow, while hiding the threads and how I run `io_context::run()` in them. And to do so, I needed a way to somehow downcast my ExecutionContext to something more familiar for my Services, to access that `io_context&`.

---

## Comment 5

> Username: ghost  
> Created at: 2020-12-30 00:58:53 UTC  
> Url: https://github.com/boostorg/asio/issues/188#issuecomment-752290978  

This issue was moved by [chriskohlhoff](https://github.com/chriskohlhoff) to [chriskohlhoff/asio#664](https://github.com/chriskohlhoff/asio/issues/664).
