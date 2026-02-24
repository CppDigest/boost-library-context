# #3050 - Preprocessor dependency can be removed [Closed]

> Username: vinniefalco  
> Created at: 2025-10-22 14:57:05 UTC  
> Updated at: 2025-10-24 09:55:23 UTC  
> Closed at: 2025-10-24 09:55:23 UTC  
> Url: https://github.com/boostorg/beast/issues/3050  

Currently core/detail/config.hpp includes preprocessor/cat.hpp. We could use our own simple macro and remove this dependency.

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2025-10-22 15:00:11 UTC  
> Updated at: 2025-10-22 15:01:56 UTC  
> Url: https://github.com/boostorg/beast/issues/3050#issuecomment-3432830142  

The `cat.hpp` was included to avoid name clashes for the source_location when assigning error_codes. The better that does not need a unique name is this:  
  
```cpp  
#define BOOST_BEAST_ASSIGN_EC(ec, error) \  
    do { \  
    static constexpr auto loc_ (BOOST_CURRENT_LOCATION); \  
    ec.assign(error, & loc_);  \  
    } while (false)  
```
