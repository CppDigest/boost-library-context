# #733 - json is not in boost.jam [Closed]

> Username: UMU618  
> Created at: 2021-08-23 15:06:20 UTC  
> Updated at: 2021-09-03 03:30:54 UTC  
> Closed at: 2021-09-03 03:30:54 UTC  
> Url: https://github.com/boostorg/build/issues/733  

I got an error:  
  
```  
error: Unable to find file or target named  
error:     '/boost//json'  
error: referred to from project at  
error:     '.'  
```  
  
https://github.com/boostorg/build/blob/d52f1993d906aa3fd3649a8dd5134402794bf86e/src/contrib/boost.jam#L215  
  
Add this?  
  
```  
boost_lib_std json                : BOOST_JSON_DYN_LINK ;  
```

---

## Comment 1

> Username: grisumbras  
> Created at: 2021-08-23 20:44:36 UTC  
> Url: https://github.com/boostorg/build/issues/733#issuecomment-904114203  

How did you get the error? The target for Boost.Json library is `/boost/json//boost_json`. By the way, this is the wrong project for this issue (the correct one is boostorg/json).

---

## Comment 2

> Username: UMU618  
> Created at: 2021-08-24 03:11:27 UTC  
> Url: https://github.com/boostorg/build/issues/733#issuecomment-904287924  

@grisumbras   
  
1. On Debian, **install** b2 and boost, and then **delete** the source.  
2. The target for Boost.Json library is `/boost/json//boost_json`, right, but it needs link from the source. I use the installed one, it should be `/boost//json`.  
3. I've tested `/boost//date_time`, `/boost//thread`, they work.

---

## Comment 3

> Username: UMU618  
> Created at: 2021-08-24 03:42:48 UTC  
> Url: https://github.com/boostorg/build/issues/733#issuecomment-904297997  

On macOS, can repro this issue: `brew install boost` now will install v1.76.0, but Boost.Json is added from 1.75, and `build/src/contrib/boost.jam` didn't add it.

---

## Comment 4

> Username: grisumbras  
> Created at: 2021-08-24 11:33:30 UTC  
> Url: https://github.com/boostorg/build/issues/733#issuecomment-904559423  

It seems, I didn't understand your issue. Are you saying, you are trying to use `build/src/contrib/boost.jam` in a project built with b2?

---

## Comment 5

> Username: UMU618  
> Created at: 2021-08-24 16:37:47 UTC  
> Url: https://github.com/boostorg/build/issues/733#issuecomment-904799478  

@grisumbras Yes. I don't like cmake, so use b2 to build my projects.  
  
This is one of my projects used b2: https://github.com/ksyun-kenc/liuguang/tree/main/src
