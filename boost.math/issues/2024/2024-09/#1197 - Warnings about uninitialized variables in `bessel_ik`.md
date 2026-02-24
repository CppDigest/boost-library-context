# #1197 - Warnings about uninitialized variables in `bessel_ik` [Open]

> Username: Lastique  
> Created at: 2024-09-12 17:26:50 UTC  
> Updated at: 2024-09-12 17:29:44 UTC  
> Url: https://github.com/boostorg/math/issues/1197  

Building Boost.Math 1.86.0 on Ubuntu 24.04, gcc 13.2 produces these warnings:  
  
```  
In file included from ./boost/math/special_functions/bessel.hpp:24,  
                 from ./boost/math/special_functions/airy.hpp:12,  
                 from ./boost/math/special_functions.hpp:15,  
                 from libs/math/build/../src/tr1/pch.hpp:9:  
./boost/math/special_functions/detail/bessel_ik.hpp: In function ‘boost_cyl_bessel_k’:  
./boost/math/special_functions/detail/bessel_ik.hpp:417:11: warning: ‘u’ may be used uninitialized [-Wmaybe-uninitialized]  
  417 |         T z = (u + n % 2);  
      |           ^  
./boost/math/special_functions/detail/bessel_ik.hpp:304:7: note: ‘u’ was declared here  
  304 |     T u, Iv, Kv, Kv1, Ku, Ku1, fv;  
      |       ^  
./boost/math/special_functions/detail/bessel_ik.hpp:417:22: warning: ‘n’ may be used uninitialized [-Wmaybe-uninitialized]  
  417 |         T z = (u + n % 2);  
      |                    ~~^~~  
./boost/math/special_functions/detail/bessel_ik.hpp:307:14: note: ‘n’ was declared here  
  307 |     unsigned n, k;  
      |              ^  
```  
  
From a brief look these look legitimate, but I didn't investigate deeply.

---

## Comment 1

> Username: mborland  
> Created at: 2024-09-12 17:29:43 UTC  
> Url: https://github.com/boostorg/math/issues/1197#issuecomment-2346859541  

Should be fixed by: https://github.com/boostorg/math/pull/1111. Further discussion is in: https://github.com/boostorg/math/pull/1181
