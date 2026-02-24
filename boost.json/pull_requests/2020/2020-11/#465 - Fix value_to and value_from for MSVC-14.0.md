# #465 Fix value_to and value_from for MSVC-14.0: [Closed]

> Username: madmongo1  
> Created at: 2020-11-17 07:17:44 UTC  
> Updated at: 2020-11-18 01:13:00 UTC  
> Closed at: 2020-11-18 01:13:00 UTC  
> Url: https://github.com/boostorg/json/pull/465  

value_to and value_from was incorrectly deducing that std::string was  
"array-like" rather than "string-like", but only on msvc-14.0  
  
Original test:  
  
template<class T, typename std::enable_if<  
    std::is_constructible<remove_cvref<T>, const char*, std::size_t>::value &&  
    std::is_convertible<decltype(std::declval<T&>().data()), const char*>::value &&  
    std::is_convertible<decltype(std::declval<T&>().size()), std::size_t>::value  
>::type* = nullptr>  
  
Which works for all compilers except msvc-14.0  
  
New test:  
  
template<class T, typename std::enable_if<  
    std::is_constructible<remove_cvref<T>, const char*, std::size_t>::value &&  
    std::is_convertible<decltype(std::declval<T&>().data()), const char*>::value &&  
    std::is_integral<decltype(std::declval<T&>().size())>::value  
>::type* = nullptr>  
  
Note that each individual test works on all compilers. It seems to be  
the conjuction of tests that caused msvc-14 to trip up.

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2020-11-17 08:33:40 UTC  
> Url: https://github.com/boostorg/json/pull/465#issuecomment-728773542  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/465/pullrequest-condensed-8b18cc6.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/465/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/465/pullrequest.html)

---
