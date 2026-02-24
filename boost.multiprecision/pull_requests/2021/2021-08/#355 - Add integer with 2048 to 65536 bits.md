# #355 Add integer with 2048 to 65536 bits [Closed]

> Username: bensuperpc  
> Created at: 2021-08-15 12:34:25 UTC  
> Updated at: 2021-08-20 19:43:48 UTC  
> Closed at: 2021-08-20 19:43:48 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/355  

Add integer with 2048 to 65536 bits.  
  
I need it for personal projects.

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2021-08-15 15:47:26 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/355#issuecomment-899069602  

I'm a bit conflicted by this, in particular a 65536-bit fixed precision cpp_int is a large object (8K per integer instance), which immediately runs the risk of stack overflow and similar issues.  You can of course add those typedefs to your own code/headers.  
  
I wonder if anyone else has any thoughts on this?  
  
BTW, for large fixed precision integers I would be inclined to use something like:  
  
```  
using int65536_t = number<cpp_int_backend<0, 65536, signed_magnitude, unchecked, std::allocator<char> > >;  
```  
  
The lower bound (0 in this case) determines how much internal memory is allocated for small integers, it's possible to increase this from zero if the default of `2*sizeof(void*)` is not enough.  Unfortunately, this doesn't currently work for unsigned types as I haven't written the code to support that :(  
  
I wonder what the rationale is for these, rather than using `cpp_int` ?

---

## Comment 2

> Username: ckormanyos  
> Created_at: 2021-08-15 18:43:12 UTC  
> Updated_at: 2021-08-15 18:44:41 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/355#issuecomment-899093985  

>> Add integer with 2048 to 65536 bits.  
  
> anyone else has any thoughts on this?  
  
Personally I would not encourage (via prefab types) the use of internally-stack-stored integers beyond a few thousand bits. In Boost we have many, many developers from diverse application domains. Simultaneously Boost.Multiprecision has been honed for quite some time now and it is working quite well in the community. So I'd actually stick with the rather cautious viewpoint since I'd anticipate more problems rather than fewer if we had prefab int65536 on stacked storage.

---

## Comment 3

> Username: jzmaddock  
> Created_at: 2021-08-15 18:45:20 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/355#issuecomment-899094237  

>So I'd actually stick with the rather cautious viewpoint since I'd anticipate more problems rather than fewer if we had prefab int65536 on the stack.  
  
My thoughts too, but I still wonder what the use case is vs arbitrary precision ints?

---

## Comment 4

> Username: bensuperpc  
> Created_at: 2021-08-15 21:33:02 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/355#issuecomment-899112647  

> Personally I would not encourage (via prefab types) the use of internally-stack-stored integers beyond a few thousand bits. In Boost we have many, many developers from diverse application domains. Simultaneously Boost.Multiprecision has been honed for quite some time now and it is working quite well in the community. So I'd actually stick with the rather cautious viewpoint since I'd anticipate more problems rather than fewer if we had prefab int65536 on stacked storage.  
  
I understand, I forgot these details, I'm still new in C ++11, I have a lot to learn ^^  
  
> ```  
> using int65536_t = number<cpp_int_backend<0, 65536, signed_magnitude, unchecked, std::allocator<char> > >;  
> ```  
  
Thank you for the info, is this as complete and reliable as in boost ?  
Are there other ways or libraries to do this sort of thing ? With good reliability  
  
> The lower bound (0 in this case) determines how much internal memory is allocated for small integers, it's possible to increase this from zero if the default of `2*sizeof(void*)` is not enough. Unfortunately, this doesn't currently work for unsigned types as I haven't written the code to support that :(  
  
It's not too important for my use case, I especially need a very large integer with good performance.  
  
At first I started to fork and update a **bigInt** library project (https://github.com/bensuperpc/bigint), But there are many problems, and it is not complete, boostorg multiprecision looks much better than this library.  
  
Do I change this PR to drop to 2048 or 4092 for example ? Or do I close the PR ?  
  
Sorry for my English, I'm French ^^

---

## Comment 5

> Username: ckormanyos  
> Created_at: 2021-08-16 05:29:35 UTC  
> Updated_at: 2021-08-16 05:31:05 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/355#issuecomment-899225493  

> other ways or libraries to do this sort of thing ? With good reliability  
  
Boost.Multiprecision is really a good way to go. Based on its testing, reliability, ease of use via header-only design it is a really good choice. Boost.Multiprecision also has good type interaction of various bit counts interacting among each other and with built-in integral types.  
  
> At first I started to fork and update a **bigInt** library project  
  
Creating or modifying an existing _big_ _integer_ class is, in fact, more of what I would consider to be an advanced development activity. It is actually harder than one might think to make a big integer class (either signed or unsigned) and get it numerically correct over a wide range of digits and also to behave as closely as possible to built-in smaller integer counterparts.  
  
So all-in-all when getting started you might want to develop your code using type definitions based on integers from Boost.Multiprecision. With Boost.Multiprecision you have a reliable choice.  
  
In the code below, I suggest several complete type definitiions that take off with 2^n bits startig with 128 bits. I decided to use, for this example, the second template parameter of `number` which turns off optimization via expression templates. The types below will, I believe behave as you expect for unsigned. You can extend these to higher bit counts and also to signe. You can let instances of these types interoperate among themselves and also among standard built-in types.  
  
```  
#include <cstdint>  
#include <iomanip>  
#include <iostream>  
  
#include <boost/multiprecision/cpp_int.hpp>  
  
using uint128_t  = boost::multiprecision::number<boost::multiprecision::cpp_int_backend<128,  128,  boost::multiprecision::unsigned_magnitude, boost::multiprecision::unchecked, void>, boost::multiprecision::et_off>;  
using uint256_t  = boost::multiprecision::number<boost::multiprecision::cpp_int_backend<256,  256,  boost::multiprecision::unsigned_magnitude, boost::multiprecision::unchecked, void>, boost::multiprecision::et_off>;  
using uint512_t  = boost::multiprecision::number<boost::multiprecision::cpp_int_backend<512,  512,  boost::multiprecision::unsigned_magnitude, boost::multiprecision::unchecked, void>, boost::multiprecision::et_off>;  
using uint1024_t = boost::multiprecision::number<boost::multiprecision::cpp_int_backend<1024, 1024, boost::multiprecision::unsigned_magnitude, boost::multiprecision::unchecked, void>, boost::multiprecision::et_off>;  
using uint2048_t = boost::multiprecision::number<boost::multiprecision::cpp_int_backend<2048, 2048, boost::multiprecision::unsigned_magnitude, boost::multiprecision::unchecked, void>, boost::multiprecision::et_off>;  
using uint4096_t = boost::multiprecision::number<boost::multiprecision::cpp_int_backend<4096, 4096, boost::multiprecision::unsigned_magnitude, boost::multiprecision::unchecked, void>, boost::multiprecision::et_off>;  
  
int main()  
{  
  uint256_t u(1234U);  
  
  u *= u;  
  u *= u;  
  u *= u;  
  u *= u;  
  
  // 1234^16 = 28909631449079534909981650669567912283424770031616  
  std::cout << u << std::endl;  
}  
```

---

## Comment 6

> Username: bensuperpc  
> Created_at: 2021-08-20 19:43:39 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/355#issuecomment-902916561  

> > other ways or libraries to do this sort of thing ? With good reliability  
>   
> Boost.Multiprecision is really a good way to go. Based on its testing, reliability, ease of use via header-only design it is a really good choice. Boost.Multiprecision also has good type interaction of various bit counts interacting among each other and with built-in integral types.  
>   
> > At first I started to fork and update a **bigInt** library project  
>   
> Creating or modifying an existing _big_ _integer_ class is, in fact, more of what I would consider to be an advanced development activity. It is actually harder than one might think to make a big integer class (either signed or unsigned) and get it numerically correct over a wide range of digits and also to behave as closely as possible to built-in smaller integer counterparts.  
>   
> So all-in-all when getting started you might want to develop your code using type definitions based on integers from Boost.Multiprecision. With Boost.Multiprecision you have a reliable choice.  
>   
> In the code below, I suggest several complete type definitiions that take off with 2^n bits startig with 128 bits. I decided to use, for this example, the second template parameter of `number` which turns off optimization via expression templates. The types below will, I believe behave as you expect for unsigned. You can extend these to higher bit counts and also to signe. You can let instances of these types interoperate among themselves and also among standard built-in types.  
>   
> ```  
> #include <cstdint>  
> #include <iomanip>  
> #include <iostream>  
>   
> #include <boost/multiprecision/cpp_int.hpp>  
>   
> using uint128_t  = boost::multiprecision::number<boost::multiprecision::cpp_int_backend<128,  128,  boost::multiprecision::unsigned_magnitude, boost::multiprecision::unchecked, void>, boost::multiprecision::et_off>;  
> using uint256_t  = boost::multiprecision::number<boost::multiprecision::cpp_int_backend<256,  256,  boost::multiprecision::unsigned_magnitude, boost::multiprecision::unchecked, void>, boost::multiprecision::et_off>;  
> using uint512_t  = boost::multiprecision::number<boost::multiprecision::cpp_int_backend<512,  512,  boost::multiprecision::unsigned_magnitude, boost::multiprecision::unchecked, void>, boost::multiprecision::et_off>;  
> using uint1024_t = boost::multiprecision::number<boost::multiprecision::cpp_int_backend<1024, 1024, boost::multiprecision::unsigned_magnitude, boost::multiprecision::unchecked, void>, boost::multiprecision::et_off>;  
> using uint2048_t = boost::multiprecision::number<boost::multiprecision::cpp_int_backend<2048, 2048, boost::multiprecision::unsigned_magnitude, boost::multiprecision::unchecked, void>, boost::multiprecision::et_off>;  
> using uint4096_t = boost::multiprecision::number<boost::multiprecision::cpp_int_backend<4096, 4096, boost::multiprecision::unsigned_magnitude, boost::multiprecision::unchecked, void>, boost::multiprecision::et_off>;  
>   
> int main()  
> {  
>   uint256_t u(1234U);  
>   
>   u *= u;  
>   u *= u;  
>   u *= u;  
>   u *= u;  
>   
>   // 1234^16 = 28909631449079534909981650669567912283424770031616  
>   std::cout << u << std::endl;  
> }  
> ```  
  
Thanks for the example, it works fine ^^

---
