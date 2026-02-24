# #171 Added python tool examples [Closed]

> Username: teeks99  
> Created at: 2017-03-09 01:52:01 UTC  
> Updated at: 2021-10-02 22:08:24 UTC  
> Closed at: 2018-01-09 15:24:37 UTC  
> Url: https://github.com/boostorg/build/pull/171  

This is an addition to the documentation that includes how to configure the python tool. I think I've put it in correctly, but I don't have the toolchain to actually build the docs and see what they look like. Can someone who does have the toolchain try that before this is merged?

---

## Review 1 [Commented]

> Username: grafikrobot  
> Created_at: 2017-03-16 02:36:50 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/build/pull/171#pullrequestreview-27244382  

📁 doc/src/reference.xml

```diff
1887 |+ : C:\\Python27-32\\include # includes
1888 |+ : C:\\Python27-32\\libs # libs
1889 |+ : <address-model>32 <address-model> # conditions - both 32 and unspecified
```

> Username: grafikrobot  
> Created_at: 2017-03-16 02:36:50 UTC  
> Updated_at: 2017-03-16 03:21:10 UTC  
> Url: https://github.com/boostorg/build/pull/171#discussion_r106328137  

I think those "<>" need to be encoded to "&lt;", "&gt;".


---

## Review 2 [Commented]

> Username: grafikrobot  
> Created_at: 2017-03-16 02:37:15 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/build/pull/171#pullrequestreview-27244420  

📁 doc/src/reference.xml

```diff
1895 |+ : C:\\Python27-64\\include # includes
1896 |+ : C:\\Python27-64\\libs # libs
1897 |+ : <address-model>64 # conditions
```

> Username: grafikrobot  
> Created_at: 2017-03-16 02:37:15 UTC  
> Updated_at: 2017-03-16 03:21:10 UTC  
> Url: https://github.com/boostorg/build/pull/171#discussion_r106328175  

"<>" need to be encoded.


---
