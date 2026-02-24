# #99 - generic version of ilog2 [Closed]

> Username: PeterSommerlad  
> Created at: 2020-12-30 10:06:27 UTC  
> Updated at: 2021-01-06 17:05:00 UTC  
> Closed at: 2021-01-06 17:05:00 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/99  

regarding:  
  
```C++  
#if 0  
    // damn! if I couldn't get this work!  If this worked I could eliminate the code above.  
    // It would shorter and slicker.   
    template<int N>  
    constexpr inline static unsigned int ilog2(const typename boost::uint_t<N>::exact & t){  
        using half_type = typename boost::uint_t<N/2>::exact;  
        constexpr const half_type upper_half = static_cast<half_type>(t >> N/2);  
        constexpr const half_type lower_half = static_cast<half_type>(t);  
        return upper_half == 0 ? ilog2(lower_half) : N/2 + ilog2(upper_half);  
    }  
#endif  
```  
  
it seems to me, the only place where you call it (didn't check thoroughly) is just below:  
  
```C++  
template<typename T>  
constexpr inline unsigned int ilog2(const T & t){  
//  log not defined for negative numbers  
//    assert(t > 0);  
    if(t == 0)  
        return 0;  
    return ilog2_detail::ilog2(  
        static_cast<  
            typename boost::uint_t<  
                bits_type<T>::value  
            >::least  
        >(t)  
    );  
}  
```  
  
Since the template argument of the generic ilog2 cannot be deduced (I suspect that is the reason, why it is not working), why not specify it directly where called, since the value `bits_type<T>::value` is available at compile time. If you already tried, just dump this issue.  
  
```C++  
template<typename T>  
constexpr inline unsigned int ilog2(const T & t){  
//  log not defined for negative numbers  
//    assert(t > 0);  
    if(t == 0)  
        return 0;  
    return ilog2_detail::ilog2<bits_type<T>::value>(  
        static_cast<  
            typename boost::uint_t<  
                bits_type<T>::value  
            >::least  
        >(t)  
    );  
}  
```

---

## Comment 1

> Username: robertramey  
> Created at: 2020-12-30 17:41:02 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/99#issuecomment-752702331  

This is a worthy idea which didn't occur to me.  I tried it and it didn't solve the problem.  I'm pretty sure it's related to the instantiation of templates with deduced types and I'm trying to do it using a "deduced" integer value.  Very confusing territory.  My current solution is pretty good in that it's transparently understandable.  The fact that it could be shorter if it exploited the rules better really bugs me so I spend a tiny bit more time on it - even though it's not really justified on an economic basis.  Sigh.  
  
Thanks for spending some time on this.  Libraries that I work on really depend on efforts invested by others to clarify, correct, and implement the more obscure corners of the library such as this one.

---

## Comment 2

> Username: robertramey  
> Created at: 2021-01-06 17:04:57 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/99#issuecomment-755430715  

Looks like I fixed this.  I failed to understand the rules for function overloads between templated and non-templated functions.  Actually I still don't understand them.  I probably could if I invested the time, but I would likely forget them again next time the issue comes up.  Oh Well.  Looks OK for now.
