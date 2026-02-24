# #102 - Need some hook that is trivially destructible [Closed]

> Username: vinipsmaker  
> Created at: 2025-12-12 17:42:36 UTC  
> Updated at: 2025-12-15 12:25:26 UTC  
> Closed at: 2025-12-15 12:25:26 UTC  
> Url: https://github.com/boostorg/intrusive/issues/102  

For one instance, I need trivial destructors (I allocate on arena and deallocate whole blocks never calling any destructors). On this scenario, I was unable to use Boost.Intrusive because all hooks I've tried (normal_link, safe_link, auto_unlink) aren't trivially destructible.  
  
Can I have a hook that is trivially destructible?

---

## Comment 1

> Username: igaztanaga  
> Created at: 2025-12-15 12:25:26 UTC  
> Url: https://github.com/boostorg/intrusive/issues/102#issuecomment-3655344438  

Thanks. This was recently fixed in issue https://github.com/boostorg/intrusive/issues/99, to be released in Boost 1.91, when using raw pointers for nodes and normal links, hooks should be trivially destructible. But it requires C++20 since triviality requires conditionally defaulted destructors using concepts. See also the following test for details:  
  
https://github.com/boostorg/intrusive/blob/develop/test/trivial_destructor_test.cpp
