# #9 - id2type_impl specialization before declaration fails to compile in MSVC with /permissive- flag [Open]

> Username: mathcounts4  
> Created at: 2019-08-15 20:53:55 UTC  
> Updated at: 2019-09-23 13:21:26 UTC  
> Url: https://github.com/boostorg/scope_exit/issues/9  

See the compilation failure [on godbolt](https://godbolt.org/z/lT4Kf_)  
  
The full specialization of id2type_impl in the full template definition of msvc_extract_type is the problem, and can easily be solved by swapping the order (full declaration before specialization definition).  
  
  
If that link is stale, go to godbolt + msvc + add flag "/permissive-" (without quotes), and paste the code below (wish we could use boost + MSVC on godbolt):  
  
```  
template<typename ID, typename T>  
struct msvc_extract_type {  
    template<>  
    struct id2type_impl<true> { // VC8.0 specific bug-feature.  
        typedef T type;  
    };  
  
    template<bool>  
    struct id2type_impl;  
  
    typedef id2type_impl<true> id2type;  
};  
```

---

## Comment 1

> Username: Pcb21  
> Created at: 2019-09-23 13:15:37 UTC  
> Url: https://github.com/boostorg/scope_exit/issues/9#issuecomment-534093730  

I am having the same problem.. indeed it is the addition of /permissive- that causes the failure. The problematic code has been copied from Boost.Typeof at some point in the past. More recent versions of Boost.Typeof just make use of decltype() on all the versions of MSVC that have /permissive- so perhaps something similar can be done here.

---

## Comment 2

> Username: MarcelRaad  
> Created at: 2019-09-23 13:21:26 UTC  
> Url: https://github.com/boostorg/scope_exit/issues/9#issuecomment-534095932  

FYI, you can work around this with `BOOST_SCOPE_EXIT_CONFIG_USE_LAMBDAS`.
