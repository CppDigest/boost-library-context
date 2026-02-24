# #1260 - `decimal128_t` fails addition edge cases that 32 and 64 do not [Closed]

> Username: mborland  
> Created at: 2025-11-24 09:24:32 UTC  
> Updated at: 2026-01-29 15:22:52 UTC  
> Closed at: 2025-12-09 13:39:59 UTC  
> Url: https://github.com/boostorg/decimal/issues/1260  

In the edge cases this fails at 128-bit:  
  
```  
dqadd71310 add 1E34  -0.51                ->  9999999999999999999999999999999999   
```  
  
But the equivalent at 64-bits runs just fine:  
```  
ddadd71310 add 1E16  -0.51                ->  9999999999999999      Inexact Rounded  
```
