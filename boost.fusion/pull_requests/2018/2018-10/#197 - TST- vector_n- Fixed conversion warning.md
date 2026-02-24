# #197 TST: vector_n: Fixed conversion warning [Merged]

> Username: Kojoley  
> Created at: 2018-10-05 12:58:09 UTC  
> Updated at: 2018-10-10 13:32:53 UTC  
> Merged at: 2018-10-10 13:31:53 UTC  
> Closed at: 2018-10-10 13:31:54 UTC  
> Url: https://github.com/boostorg/fusion/pull/197  

```  
boost\fusion\container\vector\vector.hpp(168): warning C4244: 'initializing': conversion from 'T' to 'T', possible loss of data  
1>        with  
1>        [  
1>            T=int  
1>        ]  
1>        and  
1>        [  
1>            T=float  
1>        ]  
```

---
