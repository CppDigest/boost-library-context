# #5 - gcc 4.8.2? [Closed]

> Username: vinniefalco  
> Created at: 2017-07-29 05:36:50 UTC  
> Updated at: 2017-10-21 09:27:45 UTC  
> Closed at: 2017-10-21 09:26:36 UTC  
> Url: https://github.com/boostorg/poly_collection/issues/5  

I noticed that the explicit-failures-markup.xml has:  
```  
    <!-- poly_collection -->  
...  
          <toolset name="gcc-4.*"/>  
```  
  
Are you sure you want to exclude gcc 4.8? Because it has pretty decent C++11 support, I am using it with Beast (which requires C++11).  
  
I'm excluding just these for beast:  
```  
            <toolset name="gcc-4.0.*"/>  
            <toolset name="gcc-4.1.*"/>  
            <toolset name="gcc-4.2.*"/>  
            <toolset name="gcc-4.3.*"/>  
            <toolset name="gcc-4.4.*"/>  
            <toolset name="gcc-4.5.*"/>  
            <toolset name="gcc-4.6.*"/>  
            <toolset name="gcc-4.7.*"/>  
```

---

## Comment 1

> Username: joaquintides  
> Created at: 2017-07-29 11:53:24 UTC  
> Updated at: 2017-07-29 11:56:41 UTC  
> Url: https://github.com/boostorg/poly_collection/issues/5#issuecomment-318825399  

I seem to remember GCC 4.8+ didn't work, but I've just created a PR restoring those so that we can see:  
  
https://github.com/boostorg/boost/pull/156/files  
  
BTW, I think name="gcc-4.n.\*" should rather be name="gcc-4.n\*".

---

## Comment 2

> Username: vinniefalco  
> Created at: 2017-07-29 12:34:40 UTC  
> Url: https://github.com/boostorg/poly_collection/issues/5#issuecomment-318828291  

I'll just "borrow" some of your XML :)

---

## Comment 3

> Username: joaquintides  
> Created at: 2017-10-21 09:26:36 UTC  
> Updated at: 2017-10-21 09:27:45 UTC  
> Url: https://github.com/boostorg/poly_collection/issues/5#issuecomment-338378150  

[Backported to GCC4.8-9](https://travis-ci.org/boostorg/boost/builds/290571487), wasn't straightforward.
