# #19 - -Wextra warning triggered in gcc-5 [Closed]

> Username: HDembinski  
> Created at: 2020-01-31 21:04:52 UTC  
> Updated at: 2020-05-06 13:53:05 UTC  
> Closed at: 2020-05-06 13:53:05 UTC  
> Url: https://github.com/boostorg/variant2/issues/19  

I am seeing a new warning in gcc-5 in boost.histogram, which uses boost.variant2:  
```  
../../boost/variant2/variant.hpp:1116:5: warning: base class ‘struct boost::variant2::detail::variant_base_impl<false, true, T1, T2, T3 >’ should be explicitly initialized in the copy constructor [-Wextra]  
     variant_cc_base_impl( variant_cc_base_impl const& r )  
     ^  
```  
The warning can be "fixed" by adding  
```  
        : variant_base()  
```  
in line 1118. Similar changes would then be required in line 1272.

---

## Comment 1

> Username: pdimov  
> Created at: 2020-02-01 00:01:20 UTC  
> Url: https://github.com/boostorg/variant2/issues/19#issuecomment-580961788  

Should be fixed on develop.
