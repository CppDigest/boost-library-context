# #109 - implicit semantics not clear enough [Open]

> Username: viboes  
> Created at: 2016-03-05 14:22:00 UTC  
> Updated at: 2016-03-25 20:18:51 UTC  
> Url: https://github.com/boostorg/hof/issues/109  

It is not clear to me from http://pfultz2.github.io/Fit/doc/html/implicit/index.html the semantics of implicit as the T is not part of the implicit parameters  
  
```  
assert(T(implicit<F>()(xs...)) == F<T>()(xs...));  
```  
  
IIUC, what you mean is that the the `T`conversion operator of `implicit<F>()(xs...)` behaves like `F<T>()(xs...))`  
  
I had a `implicitly` function that wrapped a type `U` and  provided an implicit conversion to any type (something similar to your `auto_cast`)  
  
```  
template<class U>  
struct implicitly_wrapper  
{  
    U x;  
    implicitly_wrapper(T x) : x(x) {}  
    template<class T>  
    operator T()  
    {  
        return T(x);  
    }  
};  
  
template<class U>  
implicitly_wrapper<decay_t<U>> implicitly(U&& u)   
{  
    return implicitly_wrapper<decay_t<U>>(std::forward<U>(u));   
}  
```  
  
Do you have other examples of use of 'implicit'?

---

## Comment 1

> Username: pfultz2  
> Created at: 2016-03-25 20:18:51 UTC  
> Url: https://github.com/boostorg/hof/issues/109#issuecomment-201476439  

> IIUC, what you mean is that the the Tconversion operator of implicit<F>()(xs...) behaves like F<T>()(xs...))  
  
Yes, that's correct. I am not sure how to make the semantics clearer.  
  
> Do you have other examples of use of 'implicit'?  
  
A `to_container` function would work to convert any range to a container, and it could deduce the container type from it get assigned to.
