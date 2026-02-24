# #259 - ODR violations when using asio::coroutine in header-only code under MSVC [Closed]

> Username: anarthal  
> Created at: 2024-05-10 11:35:28 UTC  
> Updated at: 2024-05-10 12:03:07 UTC  
> Closed at: 2024-05-10 12:03:07 UTC  
> Url: https://github.com/boostorg/mysql/issues/259  

`asio::coroutine` is implemented in terms of `__COUNTER__` under MSVC. If you define such coroutines in header-only code, and include the headers in several translation units, every TU will have a different definition of the function (as `__COUNTER__` may have different values), effectively causing an ODR violation.  
  
This has been a silent issue for some time already, but it's starting to manifest under some circumstances with MSVC under release builds.
