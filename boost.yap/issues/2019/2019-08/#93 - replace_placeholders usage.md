# #93 - replace_placeholders usage [Closed]

> Username: pkeir  
> Created at: 2019-08-05 23:05:04 UTC  
> Updated at: 2019-08-11 02:27:34 UTC  
> Closed at: 2019-08-11 02:27:34 UTC  
> Url: https://github.com/boostorg/yap/issues/93  

I'd like to use `replace_placeholders`, but I can't manage a call to it without a compilation error. For example with GCC 8.3.0:  
  
```cpp  
replace_placeholders( (1_p - 2_p) / 2_p, 3.0, 2.0 );  
```  
  
...produces a verbose message including 6 errors (listed below) relating to files within either yap or hana. I'm using yap 1.70 under 64-bit Ubuntu 19.04.  
  
```  
error: ‘value’ is not a member of ‘boost::yap::detail::rvalue_mover<true>’  
error: ‘value’ is not a member of ‘boost::yap::detail::rvalue_mover<true>’  
error: invalid use of void expression  
error: ‘void transformed_tuple’ has incomplete type  
error: invalid use of void expression  
error: ‘void transformed_tuple’ has incomplete type  
```

---

## Comment 1

> Username: pkeir  
> Created at: 2019-08-07 16:38:11 UTC  
> Url: https://github.com/boostorg/yap/issues/93#issuecomment-519175777  

This is fixed by [PR 94](https://github.com/boostorg/yap/pull/94).

---

## Comment 2

> Username: tzlaine  
> Created at: 2019-08-11 02:27:34 UTC  
> Url: https://github.com/boostorg/yap/issues/93#issuecomment-520194742  

PR applied, thanks again!
