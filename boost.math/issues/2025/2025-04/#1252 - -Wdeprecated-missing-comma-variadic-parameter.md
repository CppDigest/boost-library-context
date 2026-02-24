# #1252 - -Wdeprecated-missing-comma-variadic-parameter [Closed]

> Username: mborland  
> Created at: 2025-04-02 19:46:01 UTC  
> Updated at: 2025-04-14 15:01:00 UTC  
> Closed at: 2025-04-14 15:01:00 UTC  
> Url: https://github.com/boostorg/math/issues/1252  

The brand new clang-20 issues this warning under std=c++2c mode. Since it's a depreciation we'll need to comb through and fix all of these.  
  
```  
./boost/math/tools/traits.hpp:69:19: error: declaration of a variadic function without a comma before '...' is deprecated [-Werror,-Wdeprecated-missing-comma-variadic-parameter]  
   69 | char cdf(const D& ...);  
      |                   ^  
      |                   ,   
./boost/math/tools/traits.hpp:71:24: error: declaration of a variadic function without a comma before '...' is deprecated [-Werror,-Wdeprecated-missing-comma-variadic-parameter]  
   71 | char quantile(const D& ...);  
      |                        ^  
      |                        ,   
2 errors generated.  
```

---

## Comment 1

> Username: jzmaddock  
> Created at: 2025-04-14 11:35:12 UTC  
> Url: https://github.com/boostorg/math/issues/1252#issuecomment-2801414970  

Other than the two in traits.hpp, I only found variadic templates or zero-argument variadic functions (both of which I'm assuming don't need changing).  This looks like a pain-in-the-butt change in the std for no good reason to me, but no matter!
