# #344 - Make pair trivially move constructible [Open]

> Username: bebuch  
> Created at: 2017-05-10 12:06:39 UTC  
> Updated at: 2017-05-12 10:56:20 UTC  
> Url: https://github.com/boostorg/hana/issues/344  

On the develop branch hana::pair isn't is_trivially_move_constructible anymore! On the master branch it is.  
  
```  
#include <boost/hana.hpp>  
#include <type_traits>  
  
int main(){  
    auto p = boost::hana::make_pair(0, 0);  
    static_assert(std::is_trivially_copy_constructible< decltype(p) >::value, "");  
    static_assert(std::is_trivially_move_constructible< decltype(p) >::value, ""); // Error  
}  
```  
  
I tested with clang-TOT and GCC 7.1.1 (20170509)

---

## Comment 1

> Username: ldionne  
> Created at: 2017-05-11 00:13:26 UTC  
> Url: https://github.com/boostorg/hana/issues/344#issuecomment-300647043  

> On the master branch it is.  
  
I don't think that's true.

---

## Comment 2

> Username: bebuch  
> Created at: 2017-05-11 10:43:08 UTC  
> Url: https://github.com/boostorg/hana/issues/344#issuecomment-300752500  

I checked again and you are right. (My configuration of boost where it worked had been special.)

---

## Comment 3

> Username: bebuch  
> Created at: 2017-05-11 11:16:42 UTC  
> Url: https://github.com/boostorg/hana/issues/344#issuecomment-300759298  

Is there a good reason, why pair is not trivially move constructible? hana's tuple is.  
  
My original problem is is the following: I have an std::optional with a hana::map and want to use it in a constexpr context. With libc++ it does work fine, but libstdc++'s optional implementation doesn't work in a constexpr context, if the object is either not trivially move or copy constructible or not trivially destructible. Of course this is not the fault of hana, but if hana's pair were also trivially move constructible, it would fix my problem.  
  
A minimal example to reproduce it is the following:  
  
```  
#include <boost/hana.hpp>  
#include <optional>  
  
template < typename T >  
constexpr auto f(T&& v){  
    auto x = static_cast< T&& >(v);  
    return x;  
}  
  
int main(){  
    constexpr auto p = f(  
        std::make_optional(boost::hana::make_pair(0, 0))  
    );  
}  
```  
  
With GCC and libstdc++ you run in:  
  
```  
$ g++-7.1 -std=c++1z -I ~/media/libraries/src/boost/ make_pair.cpp                                                                                                                                                                 
In file included from make_pair.cpp:2:0:  
/home/bebuch/media/programme/gcc-7/include/c++/7.1.1/optional: In function 'int main()':  
make_pair.cpp:13:2:   in constexpr expansion of 'f(T&&) [with T = std::optional<boost::hana::pair<int, int> >]()'  
make_pair.cpp:6:7:   in constexpr expansion of 'std::optional<boost::hana::pair<int, int> >(v)'  
/home/bebuch/media/programme/gcc-7/include/c++/7.1.1/optional:451:11:   in constexpr expansion of '((std::optional<boost::hana::pair<int, int> >*)this)->std::optional<boost::hana::pair<int, int> >::<anonymous>.std::_Optional_base<boost::hana::pair<int, int> >::_Optional_base((&<anonymous>)->std::optional<boost::hana::pair<int, int> >::<anonymous>)'  
/home/bebuch/media/programme/gcc-7/include/c++/7.1.1/optional:344:37:   in constexpr expansion of '((std::_Optional_base<boost::hana::pair<int, int> >*)this)->std::_Optional_base<boost::hana::pair<int, int> >::_M_payload.std::_Optional_payload<boost::hana::pair<int, int>, false, true>::_Optional_payload(((int)__other.std::_Optional_base<boost::hana::pair<int, int> >::_M_payload.std::_Optional_payload<boost::hana::pair<int, int>, false, true>::_M_engaged), std::move<std::_Optional_payload<boost::hana::pair<int, int>, false, true>&>(__other.std::_Optional_base<boost::hana::pair<int, int> >::_M_payload))'  
/home/bebuch/media/programme/gcc-7/include/c++/7.1.1/optional:253:40: error: 'constexpr std::_Optional_payload<_Tp, false, true>::_Optional_payload(std::_Optional_payload<_Tp, false, true>&&) [with _Tp = boost::hana::pair<int, int>]' called in a constant expression  
  : _Optional_payload(std::move(__other))  
                                        ^  
/home/bebuch/media/programme/gcc-7/include/c++/7.1.1/optional:262:17: note: 'constexpr std::_Optional_payload<_Tp, false, true>::_Optional_payload(std::_Optional_payload<_Tp, false, true>&&) [with _Tp = boost::hana::pair<int, int>]' is not usable as a constexpr function because:  
       constexpr _Optional_payload(_Optional_payload&& __other)  
                 ^~~~~~~~~~~~~~~~~  
/home/bebuch/media/programme/gcc-7/include/c++/7.1.1/optional:262:17: error: 'constexpr' constructor for union 'std::_Optional_payload<boost::hana::pair<int, int>, false, true>::<unnamed union>' must initialize exactly one non-static data member  
```  
  
with:  
  
https://github.com/gcc-mirror/gcc/blob/497d987dcafcef11224e9e027b9479a20dd01702/libstdc%2B%2B-v3/include/std/optional#L103

---

## Comment 4

> Username: ldionne  
> Created at: 2017-05-11 22:20:15 UTC  
> Url: https://github.com/boostorg/hana/issues/344#issuecomment-300932750  

I just wanted to say; when I said  
  
> I don't think that's true.  
  
I did not mean "you're wrong and I don't care about this issue". I meant "I think it's also broken on master", but I did not have enough time to fully research it. I've looked into it and I think it's not trivial to add this, but I'll look into it further. Actually, I don't even understand why it works with `hana::tuple`. If this can be supported without too much complexity and too much compile-time overhead, we should do it.
