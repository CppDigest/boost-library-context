# #46 - Problems with Type Requirements [Closed]

> Username: akrzemi1  
> Created at: 2017-03-16 08:49:08 UTC  
> Updated at: 2018-09-23 22:26:36 UTC  
> Closed at: 2018-08-14 21:19:33 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/46  

In the current implementation `safe<T>` will only work with `T` being a C++ scalar type. Therefore making a general type requirements that say what operations are allowed is superfluous, and confusing (because it implies that `safe<>` is more generic.  
  
You do not need a concept to constrain anything with it, in your library. Or is the purpose of the Type requirements to show in detail what it means that `safe<T>` is a 'drop-in replacement for `T`?  
  
If you want to extend `safe<T>` for other integer types, Type requirement still need to be fixed:  
  
1. Is it really necessary that `T` can be written into a stream?  
2. In implementation you use literals -1, 0, 1 directly. Document that any T must be convertible and comparable with these literal values, or use them indirectly via overloadable traits: `Integral<T>::zero()`, `Integral<T>::one()`.  
3. Do you require that T is default-constructible? If so, do you require that thus constructed object has numeric value 0?

---

## Comment 1

> Username: robertramey  
> Created at: 2018-08-14 21:19:33 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/46#issuecomment-413019834  

OK - updated all the docs to addresss all of this - check in soon !!!
