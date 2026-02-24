# #1206 - Disabling Float128 does not work [Closed]

> Username: kursatyurt  
> Created at: 2024-10-10 10:58:57 UTC  
> Updated at: 2024-10-10 16:45:09 UTC  
> Closed at: 2024-10-10 16:45:09 UTC  
> Url: https://github.com/boostorg/math/issues/1206  

In the develop branch disabling float128 flag does not work.  
  
In my cmake file, I added flag  
  
```cmake   
add_definitions(-DBOOST_MATH_DISABLE_FLOAT128)  
```  
  
I can compile on 1.86 without any problem; however, it does not compile when I switch to the development branch  
  
```  
third_party/boost_math/include/boost/math/tools/big_constant.hpp(52): error: "T (boost::math::tools::largest_float, const char *, const std::true_type &, const std::false_type &)" contains a 128-bit floating-point, which is not supported in device code  
```  
The latest commit I can compile is `07d97df50` if I traced it correctly.

---

## Comment 1

> Username: mborland  
> Created at: 2024-10-10 16:45:09 UTC  
> Url: https://github.com/boostorg/math/issues/1206#issuecomment-2405595474  

Fixed by: #1207
