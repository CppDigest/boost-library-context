# #17 Fix forward decl lower and upper less equal [Merged]

> Username: brandon-kohn  
> Created at: 2018-09-06 13:30:35 UTC  
> Updated at: 2020-03-23 00:54:51 UTC  
> Merged at: 2020-03-23 00:54:51 UTC  
> Closed at: 2020-03-23 00:54:51 UTC  
> Url: https://github.com/boostorg/icl/pull/17  

non_empty::contains references lower_less_equal and upper_less_equal before they are declared. This patch reorders the functions so they are declared before the non_empty namespace.

---
