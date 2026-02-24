# #196 - x86 inline assembly should provide an alternative for Intel syntax [Open]

> Username: lhmouse  
> Created at: 2023-03-24 11:45:51 UTC  
> Updated at: 2023-03-24 11:45:51 UTC  
> Url: https://github.com/boostorg/interprocess/issues/196  

Instead of https://github.com/boostorg/interprocess/blob/a0c5a8ff176434c9024d4540ce092a2eebb8c5c3/include/boost/interprocess/detail/atomic.hpp#L174  
we should have  
```c  
"xadd { %1, %0 | %0, %1 }":   
```
