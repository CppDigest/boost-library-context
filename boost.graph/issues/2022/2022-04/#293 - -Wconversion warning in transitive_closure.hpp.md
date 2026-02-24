# #293 - -Wconversion warning in transitive_closure.hpp [Closed]

> Username: provtemp  
> Created at: 2022-04-30 17:15:42 UTC  
> Updated at: 2022-07-18 11:13:36 UTC  
> Closed at: 2022-07-18 11:13:36 UTC  
> Url: https://github.com/boostorg/graph/issues/293  

https://github.com/boostorg/graph/blob/c583bfa197ba324806dfb10db99c76daa8ff2fd0/include/boost/graph/transitive_closure.hpp#L85  
  
`strong_components` might return something larger than an `int` can hold. This should probably use the corresponding trait (`vertices_size_type`?) instead.

---

## Comment 1

> Username: jeremy-murphy  
> Created at: 2022-05-02 05:51:08 UTC  
> Url: https://github.com/boostorg/graph/issues/293#issuecomment-1114519477  

Sounds likely. Can you submit a patch?
