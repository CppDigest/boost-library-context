# #258 - feature request - support icu date format string directly [Open]

> Username: planetmarshall  
> Created at: 2025-01-31 12:49:37 UTC  
> Updated at: 2025-02-02 10:23:39 UTC  
> Url: https://github.com/boostorg/locale/issues/258  

We have a case where we receive date/time formatting strings in icu format - but are using `strftime`/`std::chrono` style format strings in the codebase.  
  
As I believe Boost::Locale converts `strftime` strings to ICU format when using the ICU backend, it would be useful to be able to pass the ICU format string through to the backend directly.  
  
This is something I'd be happy to work on myself if it would likely be accepted.  
  
Of course we could use ICU directly but as you've observed the API is not all that friendly.

---

## Comment 1

> Username: planetmarshall  
> Created at: 2025-02-01 09:36:47 UTC  
> Updated at: 2025-02-01 09:36:59 UTC  
> Url: https://github.com/boostorg/locale/issues/258#issuecomment-2628877157  

I was thinking of an API something like  
  
```cpp  
std::cout << as::icutime(format) << datetime  
```  
  
Assuming that boost::locale was built with the ICU backend.

---

## Comment 2

> Username: Flamefire  
> Created at: 2025-02-02 10:23:37 UTC  
> Url: https://github.com/boostorg/locale/issues/258#issuecomment-2629333619  

Doesn't look like a bad addition. However the  
> Assuming that boost::locale was built with the ICU backend.  
  
is a potential issue: What should happen if this doesn't hold? Most parts of the library have fallbacks for the different functionality on all backends but this would be very specific to a single one.  
  
However we do have precedence with the boundary iterators, so I'd accept a PR with clear enough documentation. Using an exception for the case where the ICU backend doesn't exist might be too much so probably falling back to `"datetime"` for other backends (i.e. [here](https://github.com/boostorg/locale/blob/c283206426b0d7ee3e5d1d7a5fe28263f2206abf/src/util/numeric.hpp#L102)) might be the best way.
