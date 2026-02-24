# #15 - timer/timer.hpp includes <boost/config/warning_disable.hpp> [Closed]

> Username: pgroke-dt  
> Created at: 2020-10-12 11:50:35 UTC  
> Updated at: 2020-11-20 02:15:15 UTC  
> Closed at: 2020-11-20 02:15:15 UTC  
> Url: https://github.com/boostorg/timer/issues/15  

`timer/timer.hpp` includes `<boost/config/warning_disable.hpp>`. This disabled the MSVC warning C4996 without re-enabling it again.  
  
For context: C4996 is MSVC's warning for using deprecated stuff. This means code that uses Boost.Timer will not get warning C4996 which IMO is quite bad. `<boost/config/warning_disable.hpp>` even contains the following comment:  
```  
//  IT SHOULD NOT BE INCLUDED BY ANY BOOST HEADER.  
//  
//  YOU SHOULD NOT INCLUDE IT IF YOU CAN REASONABLY FIX THE WARNING.  
```

---

## Comment 1

> Username: pdimov  
> Created at: 2020-10-12 12:29:38 UTC  
> Url: https://github.com/boostorg/timer/issues/15#issuecomment-707090605  

Removed in https://github.com/boostorg/timer/commit/dfacf29179a55559887b66e75eb8a17fee3b2185.
