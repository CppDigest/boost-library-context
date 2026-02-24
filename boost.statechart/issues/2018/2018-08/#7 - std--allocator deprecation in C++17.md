# #7 - std::allocator deprecation in C++17 [Closed]

> Username: rcdailey  
> Created at: 2018-08-24 14:09:52 UTC  
> Updated at: 2018-10-27 13:28:12 UTC  
> Closed at: 2018-10-27 13:28:12 UTC  
> Url: https://github.com/boostorg/statechart/issues/7  

In Visual Studio 2017 15.8, I am getting a warning about `std::allocator` deprecation in `boost::state_machine`, which looks like:  
  
```cpp  
template< class MostDerived,  
          class InitialState,  
          class Allocator = std::allocator< void >,  
          class ExceptionTranslator = null_exception_translator >  
class state_machine : noncopyable  
```  
  
I'm using Boost 1.58. When will this component remove usage of `std::allocator` for C++17?

---

## Comment 1

> Username: jeking3  
> Created at: 2018-08-29 03:00:07 UTC  
> Url: https://github.com/boostorg/statechart/issues/7#issuecomment-416807847  

Thanks for filing the issue here - this repository is maintained by the Community Maintenance Team.  We appreciate pull requests to fix issues like this if you have the time.  Unfortunately this is one of a few repositories that does not yet have CI integration to improve the build quality - might have caught this with that.
