# #117 - Most methods on intrusive_ptr should be constexpr, especially destructor [Closed]

> Username: RedBeard0531  
> Created at: 2025-05-07 15:45:44 UTC  
> Updated at: 2025-05-10 08:52:29 UTC  
> Closed at: 2025-05-10 08:52:29 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/117  

In C++20 destructors can now be constexpr. Without that it is impossible to even return a null `intrusive_ptr` from a constexpr function. Ideally basically every method and free function would be constexpr unless there is a good reason not to (eg the ostream `operator<<`).   
  
In our case, we have an Error type that internally uses a null intrusive_ptr to represent success. This means that constexpr functions cannot return this type even if all compile-time calls always succeed.  
  
I'd be happy to open a PR to do this, but I'm not sure what the preferred mechanism for having a method be constexpr only in C++20 is.
