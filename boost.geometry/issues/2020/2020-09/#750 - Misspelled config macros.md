# #750 - Misspelled config macros [Closed]

> Username: jefftrull  
> Created at: 2020-09-04 05:11:40 UTC  
> Updated at: 2020-10-16 14:27:48 UTC  
> Closed at: 2020-10-16 14:27:47 UTC  
> Url: https://github.com/boostorg/geometry/issues/750  

The test `algorithms/set_operations/intersection/intersection.cpp` refers to two macros, `BOOST_NO_INT64`, and `BOOST_HAS_INT64_T`, which don't exist - probably `BOOST_NO_INT64_T` is what is meant.

---

## Comment 1

> Username: ayaankhan98  
> Created at: 2020-09-23 13:18:38 UTC  
> Url: https://github.com/boostorg/geometry/issues/750#issuecomment-697358514  

https://github.com/boostorg/geometry/blob/5fe26e45b4c8fad04b6477f302343bb4b118c513/test/algorithms/set_operations/intersection/intersection.cpp#L958  
  
@jefftrull  are you refering to this?  
if so then    
if `BOOST_NO_INT64` and `BOOST_HAS_INT64_T`  don't exist then this code below  
```C++  
 #if !defined(BOOST_NO_INT64_T) || defined(BOOST_HAS_MS_INT64)   
```  
is the fix?

---

## Comment 2

> Username: jefftrull  
> Created at: 2020-09-23 15:13:10 UTC  
> Url: https://github.com/boostorg/geometry/issues/750#issuecomment-697518969  

That looks right to me!

---

## Comment 3

> Username: ashiscs  
> Created at: 2020-10-16 00:45:44 UTC  
> Url: https://github.com/boostorg/geometry/issues/750#issuecomment-709663940  

Is this issue still open to work?

---

## Comment 4

> Username: ayaankhan98  
> Created at: 2020-10-16 01:50:01 UTC  
> Url: https://github.com/boostorg/geometry/issues/750#issuecomment-709681324  

> Is this issue still open to work?  
  
@ashiscs   
already there is a PR #755 which is needed to be merged

---

## Comment 5

> Username: awulkiew  
> Created at: 2020-10-16 14:27:47 UTC  
> Url: https://github.com/boostorg/geometry/issues/750#issuecomment-710081151  

Thanks!
