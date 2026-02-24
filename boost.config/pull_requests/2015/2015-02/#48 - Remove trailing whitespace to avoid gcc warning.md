# #48 Remove trailing whitespace to avoid gcc warning [Merged]

> Username: jhunold  
> Created at: 2015-02-25 11:41:52 UTC  
> Updated at: 2015-02-25 16:58:55 UTC  
> Merged at: 2015-02-25 14:09:19 UTC  
> Closed at: 2015-02-25 14:09:19 UTC  
> Url: https://github.com/boostorg/config/pull/48  

Full warning:  
".../boost/cstdint.hpp:504:77: warning: backslash and newline separated by space  
         (defined(_ULLONG_MAX) && _ULLONG_MAX == 18446744073709551615ULL) || \ "

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2015-02-25 16:58:55 UTC  
> Url: https://github.com/boostorg/config/pull/48#issuecomment-76001614  

Thanks for catching that - merged to master as well.

---
