# #89 - Test of channel_invert<int> failing with clang 5.x and variant=release [Closed]

> Username: mloskot  
> Created at: 2018-05-14 18:13:37 UTC  
> Updated at: 2018-06-18 21:00:33 UTC  
> Closed at: 2018-06-18 15:02:33 UTC  
> Url: https://github.com/boostorg/gil/issues/89  

This issue reproduces failure of CircleCI build https://circleci.com/gh/boostorg/gil/829 with **clang 5** due to failure in `line 22` of this test targetting `channel_invert<int>`:  
  
https://github.com/boostorg/gil/blob/e9b2a6821cb01f398a14a1a222663f83233e6411/test/channel/algorithm_channel_invert.cpp#L19-L24  
  
### Minimal Working Example (in C++)  
  
```  
#include <limits>  
#include <iostream>  
#include <typeinfo>  
  
template <typename C>  
inline C channel_invert(C x)  
{  
    return std::numeric_limits<C>::max() - x + std::numeric_limits<C>::min();  
}  
  
template <typename C>  
inline C channel_invert2(C x)  
{  
    // alternative equivalent implementation  
    return (x - std::numeric_limits<C>::max()) * (-1) + std::numeric_limits<C>::min();  
}  
  
template <typename C>  
void test()  
{  
    std::cout << "--- C type: " << typeid(C).name() << std::endl;  
  
    C x1{0}, x2{0};  
    x1 = channel_invert(std::numeric_limits<C>::min());  
    x2 = channel_invert2(std::numeric_limits<C>::min());  
    std::cout << x1 << std::endl;  
    std::cout << x2 << std::endl;  
}  
  
int main()  
{  
    test<int>();  
    test<unsigned int>();  
    test<short>();  
    test<unsigned short>();  
}  
```  
  
### Actual behavior  
  
- clang 5 - notice the negative one for `int`  
  
```  
$ clang++ --version  
clang version 5.0.0-3~16.04.1 (tags/RELEASE_500/final)  
$ clang++ -std=c++11 -O2 test_channel_invert_for_int.cpp  
$ ./a.out  
--- C type: i  
-1  
-1  
--- C type: j  
4294967295  
4294967295  
--- C type: s  
32767  
32767  
--- C type: t  
65535  
65535  
```  
  
### Expected  behavior  
  
- GCC 7  
  
```  
$ g++ --version  
g++ (Ubuntu 7.3.0-16ubuntu3~16.04.1) 7.3.0  
$ g++ -std=c++11 -O2 test_channel_invert_for_int.cpp  
$ ./a.out  
--- C type: i  
2147483647  
2147483647  
--- C type: j  
4294967295  
4294967295  
--- C type: s  
32767  
32767  
--- C type: t  
65535  
65535  
```  
  
- GCC 5  
  
```  
$ g++-5 --version  
g++-5 (Ubuntu 5.5.0-12ubuntu1~16.04) 5.5.0 20171010  
$ g++-5 -std=c++11 -O2 test_channel_invert_for_int.cpp  
$ ./a.out  
--- C type: i  
2147483647  
2147483647  
--- C type: j  
4294967295  
4294967295  
--- C type: s  
32767  
32767  
--- C type: t  
65535  
65535  
```  
  
### Question  
  
For `x = -2147483648`, this value inverting expression  
  
```  
std::numeric_limits<C>::max() - x + std::numeric_limits<C>::min();  
```  
  
does seem to promote `int` to `unsigned int` at least due to the partial value of `2147483647 - (-2147483648) = 4294967295`   
  
Let's consider that:  
- The `channel_invert` tests are not failing for Travis CI or AppVeyor.  
- The CircleCI build workflow targets 18 GCC/clang versions in total, see latest workflow results at https://circleci.com/workflow-run/3a14dd64-6c38-46b2-a6da-678c0075ca27.  
- Only **clang 5.0** is failing.   
  
Are we experiencing a compiler bug or nasty UB? Your bets @chhenning @stefanseefeld?  
  
### References  
  
This may be directly related to the issue #51

---

## Comment 1

> Username: chhenning  
> Created at: 2018-05-14 18:32:12 UTC  
> Url: https://github.com/boostorg/gil/issues/89#issuecomment-388917778  

Can you reduce the code sample to just the "-1" result? We could take it to the boost ml then.

---

## Comment 2

> Username: mloskot  
> Created at: 2018-05-14 18:54:13 UTC  
> Updated at: 2018-05-14 18:54:45 UTC  
> Url: https://github.com/boostorg/gil/issues/89#issuecomment-388924537  

I really hoped that is quite a minimal sample, but if you want just the `-1`, you can remove these three calls:  
  
```  
test<unsigned int>();  
test<short>();  
test<unsigned short>();  
```

---

## Comment 3

> Username: chhenning  
> Created at: 2018-05-14 19:04:01 UTC  
> Url: https://github.com/boostorg/gil/issues/89#issuecomment-388927501  

Sorry to be so annoying. I was thinking of just a one or two liner without any function call. Sometimes the problem goes away...

---

## Comment 4

> Username: mloskot  
> Created at: 2018-05-18 21:40:24 UTC  
> Updated at: 2018-05-18 21:42:35 UTC  
> Url: https://github.com/boostorg/gil/issues/89#issuecomment-390339866  

>  Sometimes the problem goes away...  
  
That is exactly the reason why I stick to the function template(s). This should make it more clear:  
  
```  
#include <limits>  
#include <iostream>  
#include <typeinfo>  
  
template <typename C>  
inline C channel_invert1(C x)  
{  
    return std::numeric_limits<C>::max() - x + std::numeric_limits<C>::min();  
}  
  
template <typename C>  
inline C channel_invert2(C x)  
{  
    return (x - std::numeric_limits<C>::max()) * (-1) + std::numeric_limits<C>::min();  
}  
  
int main()  
{  
    int x = std::numeric_limits<int>::min();  
    std::cout << x << std::endl;  
  
    // plain expressions  
    int x_invert1 = std::numeric_limits<int>::max() - x + std::numeric_limits<int>::min();  
    int x_invert2 = (x - std::numeric_limits<int>::max()) * (-1) + std::numeric_limits<int>::min();  
    std::cout << x_invert1 << std::endl;  
    std::cout << x_invert2 << std::endl;  
  
    // the same expressions wrapped in function template  
    std::cout << channel_invert1<int>(x) << std::endl;  
    std::cout << channel_invert2<int>(x) << std::endl;  
}  
```  
  
The sample output of this program compiled with clang 5.0.0 with optimization:  
  
```  
-2147483648  
2147483647  
2147483647  
-1  
-1  
```

---

## Comment 5

> Username: mloskot  
> Created at: 2018-05-19 19:40:17 UTC  
> Url: https://github.com/boostorg/gil/issues/89#issuecomment-390427743  

I posted this sample to clang mailing list [[cfe-users] Boost.GIL test failing with clang 5.x while pass with 15 gcc/clang versions](http://lists.llvm.org/pipermail/cfe-users/2018-May/001335.html).

---

## Comment 6

> Username: chhenning  
> Created at: 2018-05-19 21:22:59 UTC  
> Url: https://github.com/boostorg/gil/issues/89#issuecomment-390433577  

Good idea! I don't have clang right now. Hopefully someone will take a look.

---

## Comment 7

> Username: mloskot  
> Created at: 2018-05-20 18:03:32 UTC  
> Url: https://github.com/boostorg/gil/issues/89#issuecomment-390499977  

I also posted it to Boost developer's list https://lists.boost.org/Archives/boost/2018/05/242170.php

---

## Comment 8

> Username: chhenning  
> Created at: 2018-05-20 18:52:37 UTC  
> Url: https://github.com/boostorg/gil/issues/89#issuecomment-390502974  

Yeah, saw that. Now all we need is just on answer. ;-) Probably a compiler bug, right?

---

## Comment 9

> Username: mloskot  
> Created at: 2018-05-21 17:52:33 UTC  
> Updated at: 2018-05-21 17:53:06 UTC  
> Url: https://github.com/boostorg/gil/issues/89#issuecomment-390731129  

We've got one response to the cfe-users thread: http://lists.llvm.org/pipermail/cfe-users/2018-May/001336.html.  
  
My suspicions about the bug in GIL seem to be confirmed:  
  
> BTW This is a perfect opportunity to try out UndefinedBehaviorSanitizer! https://clang.llvm.org/docs/UndefinedBehaviorSanitizer.html  
> ```  
> clang++ -fsanitize=signed-integer-overflow overflow.cpp  
> ./a.out  
> -2147483648  
> overflow.cpp:24:52: runtime error: signed integer overflow: 2147483647 - -2147483648 cannot be represented in type 'int'  
> ```  
  
I think I mentioned that earlier somewhere, via e-mail or on GIL ml, we may need some integral type selectors and explicit conversions to larger types.  
  
For example, something along these concepts:  
https://github.com/boostorg/geometry/blob/develop/include/boost/geometry/util/promote_integral.hpp  
https://github.com/boostorg/geometry/blob/develop/include/boost/geometry/util/select_most_precise.hpp  
  
Finally, we should enable the sanitizers for clang and/or GCC builds on CI services.

---

## Comment 10

> Username: stefanseefeld  
> Created at: 2018-05-21 18:23:54 UTC  
> Url: https://github.com/boostorg/gil/issues/89#issuecomment-390739884  

On 05/21/18 13:52, Mateusz Loskot wrote:  
>  
> We've got one response to the cfe-users thread:  
> http://lists.llvm.org/pipermail/cfe-users/2018-May/001336.html.  
>  
> My suspicions about the bug in GIL seem to be confirmed:  
>  
>     BTW This is a perfect opportunity to try out  
>     UndefinedBehaviorSanitizer!  
>     https://clang.llvm.org/docs/UndefinedBehaviorSanitizer.html  
>  
>     |clang++ -fsanitize=signed-integer-overflow overflow.cpp ./a.out  
>     -2147483648 overflow.cpp:24:52: runtime error: signed integer  
>     overflow: 2147483647 - -2147483648 cannot be represented in type  
>     'int'|  
>  
  
Excellent find !  
  
>     ||  
>  
> I think I mentioned that earlier somewhere, via e-mail or on GIL ml,  
> we may need some integral type selectors and explicit conversions to  
> larger types.  
>  
> For example, something along these concepts:  
> https://github.com/boostorg/geometry/blob/develop/include/boost/geometry/util/promote_integral.hpp  
> https://github.com/boostorg/geometry/blob/develop/include/boost/geometry/util/select_most_precise.hpp  
>  
  
I fully agree. These seem simple enough (and self-contained) that it  
might make sense to simply clone them into the GIL repo.

---

## Comment 11

> Username: mloskot  
> Created at: 2018-05-22 08:36:28 UTC  
> Url: https://github.com/boostorg/gil/issues/89#issuecomment-390908931  

@stefanseefeld Good. I will work on integrating the type selectors into GIL.

---

## Comment 12

> Username: mloskot  
> Created at: 2018-06-18 20:49:53 UTC  
> Url: https://github.com/boostorg/gil/issues/89#issuecomment-398191001  

I'm happy to confirm the UB due to signed integer overflow has been now fixed by #94 and the clang 5.0 build tests pass https://circleci.com/workflow-run/f79210be-b033-402c-aefb-6461d897c88a

---

## Comment 13

> Username: chhenning  
> Created at: 2018-06-18 21:00:32 UTC  
> Url: https://github.com/boostorg/gil/issues/89#issuecomment-398194151  

@mloskot Congrats!
