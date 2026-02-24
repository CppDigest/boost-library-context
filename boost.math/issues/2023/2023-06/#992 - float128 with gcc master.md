# #992 - float128 with gcc master [Closed]

> Username: sloriot  
> Created at: 2023-06-06 15:50:03 UTC  
> Updated at: 2023-06-08 07:08:53 UTC  
> Closed at: 2023-06-08 07:08:53 UTC  
> Url: https://github.com/boostorg/math/issues/992  

Hi,  
  
with gcc@ce2188e4320cbb46d6246bd3f478ba20440c62f3, I have the following error during compilation:  
  
```  
boost_1_82_0/include/boost/math/cstdfloat/cstdfloat_limits.hpp:46:13: error: redefinition of ‘class std::numeric_limits<__float128>’  
   46 |       class numeric_limits<boost::math::cstdfloat::detail::float_internal128_t>  
```  
  
I guess it has to do with `BOOST_CSTDFLOAT_HAS_INTERNAL_FLOAT128_T` being not correctly defined after gcc's update or something like that. I only tested boost 1.82 (sorry if it has already been fixed in HEAD).  
  
cc @afabri

---

## Comment 1

> Username: sloriot  
> Created at: 2023-06-06 15:54:39 UTC  
> Url: https://github.com/boostorg/math/issues/992#issuecomment-1579031424  

minimal example:  
```  
#include <boost/math/cstdfloat/cstdfloat_limits.hpp>  
  
int main()  
{}  
```

---

## Comment 2

> Username: mborland  
> Created at: 2023-06-06 15:59:09 UTC  
> Url: https://github.com/boostorg/math/issues/992#issuecomment-1579040512  

> minimal example:  
>   
> ```  
> #include <boost/math/cstdfloat/cstdfloat_limits.hpp>  
>   
> int main()  
> {}  
> ```  
  
I don't see it with GCC 13.1 on godbolt: https://godbolt.org/z/h39GTMz16.

---

## Comment 3

> Username: sloriot  
> Created at: 2023-06-06 16:02:08 UTC  
> Url: https://github.com/boostorg/math/issues/992#issuecomment-1579045505  

that's gcc master targeting gcc 14

---

## Comment 4

> Username: mborland  
> Created at: 2023-06-06 16:04:16 UTC  
> Url: https://github.com/boostorg/math/issues/992#issuecomment-1579049806  

> that's gcc master targeting gcc 14  
  
That's fun. Thanks for the heads up.

---

## Comment 5

> Username: afabri  
> Created at: 2023-06-07 05:47:57 UTC  
> Url: https://github.com/boostorg/math/issues/992#issuecomment-1579943709  

x86-64 gcc (trunk)   in godbolt, right?

---

## Comment 6

> Username: mborland  
> Created at: 2023-06-07 07:27:45 UTC  
> Url: https://github.com/boostorg/math/issues/992#issuecomment-1580096218  

> x86-64 gcc (trunk) in godbolt, right?  
  
Yes, and it does reproduce the error.
