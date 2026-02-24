# #11 - Namespace not changed by bcp in file detail/implementation.hpp [Closed]

> Username: joaoluispinto  
> Created at: 2019-03-27 11:03:14 UTC  
> Updated at: 2023-02-21 21:24:05 UTC  
> Closed at: 2023-02-21 21:24:04 UTC  
> Url: https://github.com/boostorg/unordered/issues/11  

When using bcp in version 1.69.0 to move boost to a different designated namespace, file boost/unordered/detail/implementation.hpp does not get changed and requires manual fixing.  
  
Near line 1603 of the aforementioned file, the several uses of macro BOOST_UNORDERED_CONSTRUCT_FROM_TUPLE keep on using the original namespace.

---

## Comment 1

> Username: cmazakas  
> Created at: 2021-12-07 21:24:50 UTC  
> Url: https://github.com/boostorg/unordered/issues/11#issuecomment-988273186  

Hey @joaoluispinto, sorry for the late reply here.  
  
I've attempted to recreate your issue using the latest `bcp` and it _seems_ like it should be working now. Namely, my output in `implementation.hpp` looked like this:  
```cpp  
        BOOST_UNORDERED_CONSTRUCT_FROM_TUPLE(1, 1, foo)  
        BOOST_UNORDERED_CONSTRUCT_FROM_TUPLE(1, 2, foo)  
        BOOST_UNORDERED_CONSTRUCT_FROM_TUPLE(1, 3, foo)  
        BOOST_UNORDERED_CONSTRUCT_FROM_TUPLE(1, 4, foo)  
        BOOST_UNORDERED_CONSTRUCT_FROM_TUPLE(1, 5, foo)  
        BOOST_UNORDERED_CONSTRUCT_FROM_TUPLE(1, 6, foo)  
        BOOST_UNORDERED_CONSTRUCT_FROM_TUPLE(1, 7, foo)  
        BOOST_UNORDERED_CONSTRUCT_FROM_TUPLE(1, 8, foo)  
        BOOST_UNORDERED_CONSTRUCT_FROM_TUPLE(1, 9, foo)  
        BOOST_UNORDERED_CONSTRUCT_FROM_TUPLE(1, 10, foo)  
```  
  
This was done using:  
```console  
exbigboss@DESKTOP-I99BGOL:~/cpp/boost-root$ ./bin.v2/tools/bcp/gcc-9/release/link-static/bcp --namespace=foo unordered rawr  
```  
  
Let me know if the above is incorrect or doesn't reliably replicate your issue.

---

## Comment 2

> Username: cmazakas  
> Created at: 2023-02-21 21:24:04 UTC  
> Url: https://github.com/boostorg/unordered/issues/11#issuecomment-1439111444  

Alright, because there's been no response for quite some time. I'm going to go ahead and close this issue as I couldn't reproduce it.
