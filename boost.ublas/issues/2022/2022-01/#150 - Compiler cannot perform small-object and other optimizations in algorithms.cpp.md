# #150 - Compiler cannot perform small-object and other optimizations in algorithms.cpp [Closed]

> Username: amitsingh19975  
> Created at: 2022-01-15 17:31:06 UTC  
> Updated at: 2022-02-06 18:20:35 UTC  
> Closed at: 2022-02-06 18:20:35 UTC  
> Url: https://github.com/boostorg/ublas/issues/150  

We are currently using `std::function` to recurse. It can be avoided by simply providing the trailing return type and passing the lambda through one of its arguments.   
There is are two main disadvantages of using `std::function`:  
- Uses heap allocation if the compiler does not implement `Small-Object Optimization` ( May vary from compiler to compiler ).  
- It invokes the function through a function pointer that could hinder possible optimizations by the compiler.  
  
## Current implementation  
```cpp  
ValueType accumulate(SizeType const p, SizeType const*const n,  
           PointerIn a, SizeType const*const w,  
           ValueType k)  
{  
...  
  std::function<ValueType(SizeType r, PointerIn a, ValueType k)> lambda;  
  
  lambda = [&lambda, n, w](SizeType r, PointerIn a, ValueType k)  
  {  
    if(r > 0u)  
      for(auto d = 0u; d < n[r]; a += w[r], ++d)  
        k = lambda(r-1, a, k);  
    else  
      for(auto d = 0u; d < n[0]; a += w[0], ++d)  
        k += *a;  
    return k;  
  };  
  
  return lambda( p-1, a,  k );  
}  
```  
  
## Improved implementation  
```cpp  
ValueType accumulate(SizeType const p, SizeType const*const n,  
           PointerIn a, SizeType const*const w,  
           ValueType k)  
{  
...  
  auto lambda = [n, w](auto const& self, SizeType r, PointerIn a, ValueType k)  
    -> ValueType  
  {  
    if(r > 0u)  
      for(auto d = 0u; d < n[r]; a += w[r], ++d)  
        k = self(self,r-1, a, k);  
    else  
      for(auto d = 0u; d < n[0]; a += w[0], ++d)  
        k += *a;  
    return k;  
  };  
  
  return lambda(lambda, p-1, a,  k );  
}  
```

---

## Comment 1

> Username: Neel-Shah-29  
> Created at: 2022-01-16 10:59:57 UTC  
> Url: https://github.com/boostorg/ublas/issues/150#issuecomment-1013854007  

I am interested in solving this issue. Kindly assign it to me.

---

## Comment 2

> Username: Neel-Shah-29  
> Created at: 2022-01-16 11:17:59 UTC  
> Url: https://github.com/boostorg/ublas/issues/150#issuecomment-1013856759  

Hello, just a question here i need to change all the ```std::function``` in algorithms.hpp file and use the current implementation discussed above right?  
I found approximately 6 ```std::function``` , i need to change all of them right?

---

## Comment 3

> Username: amitsingh19975  
> Created at: 2022-01-16 11:23:06 UTC  
> Url: https://github.com/boostorg/ublas/issues/150#issuecomment-1013857486  

> Hello, just a question here i need to change all the `std::function` in algorithms.hpp file and use the current implementation discussed above right?  
> I found approximately 6 `std::function` , i need to change all of them right?  
  
Yes, once @bassoy is back. We need to take his thoughts on this issue and then merge the pull request.

---

## Comment 4

> Username: Neel-Shah-29  
> Created at: 2022-01-16 11:32:21 UTC  
> Url: https://github.com/boostorg/ublas/issues/150#issuecomment-1013858835  

yes sure i will add a Pull request soon . Kindly check it and if any mistakes are there notify me here i will be happy to help and correct them from myside. Thanks!

---

## Comment 5

> Username: Neel-Shah-29  
> Created at: 2022-01-16 14:19:46 UTC  
> Url: https://github.com/boostorg/ublas/issues/150#issuecomment-1013884718  

I have created a pull request. Kindly review it. Thanks!
