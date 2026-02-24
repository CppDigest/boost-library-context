# #229 - Request new config macro to detect if basic_iostream has operator void* or not [Closed]

> Username: jeking3  
> Created at: 2018-07-17 11:52:32 UTC  
> Updated at: 2018-07-17 12:14:24 UTC  
> Closed at: 2018-07-17 12:14:24 UTC  
> Url: https://github.com/boostorg/config/issues/229  

I'm looking for a Boost.Config macro that will allow this to build (on Windows too):  
```  
#if __cplusplus < 201103L  
    BOOST_CHECK_MESSAGE(0 == str.operator void* (),  
            "stream does not report failure by operator void* (up to C++11)");  
#else  
    BOOST_CHECK_MESSAGE(0 == str.operator bool (),  
            "stream does not report failure by operator bool (as of C++11)");  
#endif  
```

---

## Comment 1

> Username: jzmaddock  
> Created at: 2018-07-17 12:02:22 UTC  
> Url: https://github.com/boostorg/config/issues/229#issuecomment-405557734  

We don't have a macro for this - do we need one - doesn't either a `static_cast` to `bool` of use in an if-statement work in either case?

---

## Comment 2

> Username: jeking3  
> Created at: 2018-07-17 12:14:22 UTC  
> Url: https://github.com/boostorg/config/issues/229#issuecomment-405561069  

It does, so I will close this.  I guess that was the original intention for the change in C++11 to be backwards compatible.  Thanks for the suggestion.
