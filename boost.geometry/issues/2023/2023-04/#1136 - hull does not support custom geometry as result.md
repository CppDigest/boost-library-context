# #1136 - hull does not support custom geometry as result [Open]

> Username: barendgehrels  
> Created at: 2023-04-19 16:33:21 UTC  
> Updated at: 2023-06-19 14:04:15 UTC  
> Url: https://github.com/boostorg/geometry/issues/1136  

as detected in new unit test `custom_ring.cpp`

---

## Comment 1

> Username: vissarion  
> Created at: 2023-06-19 14:04:15 UTC  
> Url: https://github.com/boostorg/geometry/issues/1136#issuecomment-1597251695  

Shouldn't the `cnc_container` expose its value type by something like  
  
`using value_type = Item;`
