# #66 - `is_final` doesn't work with template types on gcc 6.4.0 [Closed]

> Username: danieljames  
> Created at: 2018-04-06 10:14:39 UTC  
> Updated at: 2018-05-08 17:37:15 UTC  
> Closed at: 2018-05-08 17:37:15 UTC  
> Url: https://github.com/boostorg/type_traits/issues/66  

For this code:  
  
    #include <boost/type_traits/is_final.hpp>  
    #include <type_traits>  
    #include <iostream>  
  
    struct final1 final {};  
    template <typename T> struct final2 final {};  
  
    int main() {  
        std::cout << "boost::is_final<final1>: " << boost::is_final<final1>::value << std::endl;  
        std::cout << "boost::is_final<final2<int>>::value: " << boost::is_final<final2<int>>::value << std::endl;  
        std::cout << "std::is_final<final1>: " << std::is_final<final1>::value << std::endl;  
        std::cout << "std::is_final<final2<int>>::value: " << std::is_final<final2<int>>::value << std::endl;  
    }  
  
GCC 6.4.0 outputs:  
  
    boost::is_final<final1>: 1  
    boost::is_final<final2<int>>::value: 0  
    std::is_final<final1>: 1  
    std::is_final<final2<int>>::value: 1  
  
I'd expect all values to be `1`. I get that for Clang.

---

## Comment 1

> Username: Lastique  
> Created at: 2018-04-06 10:34:27 UTC  
> Url: https://github.com/boostorg/type_traits/issues/66#issuecomment-379215294  

The test fails with gcc 7.2 as well. This is weird, `std::is_final` is defined the same way as `boost::is_final`, only without `remove_cv`. Removing `remove_cv` doesn't fix it. `__is_final` intrinsic reports the correct result all the time. Looks like a compiler bug to me.

---

## Comment 2

> Username: Lastique  
> Created at: 2018-04-06 10:52:29 UTC  
> Url: https://github.com/boostorg/type_traits/issues/66#issuecomment-379218942  

Nevermind that, I'm failing miserably today for some reason.  
  
Removing `remove_cv` *does* fix the problem for gcc 6 and 7.

---

## Comment 3

> Username: Lastique  
> Created at: 2018-04-06 12:18:56 UTC  
> Url: https://github.com/boostorg/type_traits/issues/66#issuecomment-379236170  

I've reported this bug:  
  
https://gcc.gnu.org/bugzilla/show_bug.cgi?id=85254

---

## Comment 4

> Username: jzmaddock  
> Created at: 2018-05-08 17:37:15 UTC  
> Url: https://github.com/boostorg/type_traits/issues/66#issuecomment-387482510  

This should be fixed in develop now.
