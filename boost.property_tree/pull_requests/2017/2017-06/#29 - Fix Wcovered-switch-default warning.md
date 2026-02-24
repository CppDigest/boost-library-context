# #29 Fix Wcovered-switch-default warning [Merged]

> Username: garyfurnish  
> Created at: 2017-06-08 00:07:18 UTC  
> Updated at: 2023-11-24 16:22:05 UTC  
> Merged at: 2023-11-24 16:19:27 UTC  
> Closed at: 2023-11-24 16:19:27 UTC  
> Url: https://github.com/boostorg/property_tree/pull/29  

On clang with -Wcovered-switch-default this fixes the following error:  
`/mnt/fscratch0/pub/dependencies/boost/boost/property_tree/json_parser/detail/standard_callbacks.hpp:130:6: error: default label in switch which covers all enumeration values [-Werror,-Wcovered-switch-default]  
            default:`  
  
Essentially default is unnecessary and may prevent detection of future bugs by allowing catching future added enums.  This moves the Assert to the bottom of the function where it theoretically is dead code as the enum is fully covered by returns.

---

## Comment 1

> Username: pdimov  
> Created_at: 2023-11-21 22:56:04 UTC  
> Url: https://github.com/boostorg/property_tree/pull/29#issuecomment-1821828121  

Closing/reopening to retrigger CI.

---

## Comment 2

> Username: pdimov  
> Created_at: 2023-11-21 23:49:58 UTC  
> Url: https://github.com/boostorg/property_tree/pull/29#issuecomment-1821873422  

Since BOOST_ASSERT disappears in release builds, this change results in a "not all control paths return a value" warning.

---

## Comment 3

> Username: ashtum  
> Created_at: 2023-11-22 05:14:21 UTC  
> Updated_at: 2023-11-22 05:15:24 UTC  
> Url: https://github.com/boostorg/property_tree/pull/29#issuecomment-1822127154  

> Since BOOST_ASSERT disappears in release builds, this change results in a "not all control paths return a value" warning.  
  
This should be the case for the current code as well, because it relies on BOOST_ASSERT in these two cases:  
```C++  
            case object:  
            default:  
                BOOST_ASSERT(false); // must start with string, i.e. call new_value  
```

---

## Comment 4

> Username: pdimov  
> Created_at: 2023-11-22 11:25:02 UTC  
> Url: https://github.com/boostorg/property_tree/pull/29#issuecomment-1822589101  

It's not, as the CI results show. The code just falls through to the next `case`.  
  
This PR can probably be fixed to be warning-free by adding `BOOST_UNREACHABLE_RETURN(root)` at the end.

---
