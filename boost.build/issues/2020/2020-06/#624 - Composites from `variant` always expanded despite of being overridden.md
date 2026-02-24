# #624 - Composites from `variant` always expanded despite of being overridden [Open]

> Username: Kojoley  
> Created at: 2020-06-14 16:27:14 UTC  
> Updated at: 2021-05-29 17:22:31 UTC  
> Url: https://github.com/boostorg/build/issues/624  

```  
import feature ;  
feature.feature foo  
    : on off  
    : propagated composite ;  
  
feature.compose <foo>on : <define>FOO_ON ;  
feature.compose <foo>off : <define>FOO_OFF ;  
  
variant bar : release : <foo>on ;  
  
exe test : test.cpp : <variant>bar <foo>off ;  
  
#or:  
#exe test : test.cpp : <foo>off ;  
#>b2 <variant>bar  
```  
  
Compiles with `-DFOO_OFF -DFOO_ON`  
  
```  
exe simple : simple.cpp : <foo>off ;  
>b2 foo=on  
```  
  
Correctly compiles with `-DFOO_OFF` only

---

## Comment 1

> Username: stale[bot]  
> Created at: 2021-05-29 17:22:02 UTC  
> Url: https://github.com/boostorg/build/issues/624#issuecomment-850868256  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2  
This issue has been automatically marked as "transition" to indicate the potential for needing transition to the new B2 development project.
