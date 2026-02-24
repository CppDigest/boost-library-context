# #27 - make Boost.Python respect alignment of types [Open]

> Username: nevion  
> Created at: 2015-07-27 05:03:59 UTC  
> Updated at: 2021-04-17 21:00:12 UTC  
> Url: https://github.com/boostorg/python/issues/27  

Currently if a type has an alignment, such as   
  
```  
struct alignas(32){  
    double pos[3];  
};  
```  
  
Boost.Python will merrily proceed to violate the alignment required by the type and place at any address in memory.  This makes a big problem for sharing code utilizing vectorization (SSE) and a common example of this is the passing around of Eigen types.

---

## Comment 1

> Username: eudoxos  
> Created at: 2015-09-27 11:14:57 UTC  
> Url: https://github.com/boostorg/python/issues/27#issuecomment-143543781  

Great work, thanks. Hope to see it merged soon.

---

## Comment 2

> Username: stefanseefeld  
> Created at: 2021-04-17 21:00:12 UTC  
> Url: https://github.com/boostorg/python/issues/27#issuecomment-821886896  

As a workaround I suggest using a smart-pointer as `HeldType` (https://www.boost.org/doc/libs/1_76_0/libs/python/doc/html/reference/high_level_components.html#high_level_components.boost_python_class_hpp.class_template_class_t_bases_hel), which allocates the object on the heap, rather than using the placement new operator to force the object onto a specific (and potentially mis-aligned) memory location within the pre-allocated Python object.
