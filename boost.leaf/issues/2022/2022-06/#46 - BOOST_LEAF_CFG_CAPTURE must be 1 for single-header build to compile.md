# #46 - BOOST_LEAF_CFG_CAPTURE must be 1 for single-header build to compile [Closed]

> Username: dnmiller  
> Created at: 2022-06-21 23:48:42 UTC  
> Updated at: 2022-07-08 04:15:51 UTC  
> Closed at: 2022-06-29 10:08:17 UTC  
> Url: https://github.com/boostorg/leaf/issues/46  

If `BOOST_LEAF_CFG_CAPTURE` is 0, then `<memory>` isn't included, but the code that uses `std::shared_ptr` is not stripped out by the preprocessor, causing a build failure.  
  
This is from a single-header build with this at the top:  
`// Generated on 05/27/2022 from https://github.com/boostorg/leaf/tree/5e9a5b9.`

---

## Comment 1

> Username: zajo  
> Created at: 2022-06-22 02:21:15 UTC  
> Url: https://github.com/boostorg/leaf/issues/46#issuecomment-1162553828  

Thanks for reporting this.

---

## Comment 2

> Username: zajo  
> Created at: 2022-06-22 06:31:10 UTC  
> Url: https://github.com/boostorg/leaf/issues/46#issuecomment-1162699743  

I see a bug in LEAF where if you have `BOOST_LEAF_CFG_CAPTURE=0`, it attempts to use `std::shared_ptr` in `exception.hpp`, which leads to a compile error. However, everything in `exception.hpp` is turned off if exceptions are disabled. I'll fix this, but can you confirm that the configuration I'm describing is what you're using when you get the error?

---

## Comment 3

> Username: dnmiller  
> Created at: 2022-06-22 15:06:38 UTC  
> Url: https://github.com/boostorg/leaf/issues/46#issuecomment-1163230307  

Yep, that's it:  
```c++  
boost/leaf/exception.hpp:52:14: error: 'shared_ptr' in namespace 'std' does not name a template type  
boost/leaf/exception.hpp:52:9: note: 'std::shared_ptr' is defined in header '<memory>'; did you forget to '#include <memory>'?  
```

---

## Comment 4

> Username: zajo  
> Created at: 2022-06-22 18:08:39 UTC  
> Url: https://github.com/boostorg/leaf/issues/46#issuecomment-1163450629  

Yeah, OK. I'll remove the `shared_ptr` from there, it was rather inefficient anyway. I'm curious about your platform if you don't mind me asking, it's surprising to want `BOOST_LEAF_CFG_CAPTURE=0` but also not disable exception handling.

---

## Comment 5

> Username: zajo  
> Created at: 2022-06-29 10:08:17 UTC  
> Url: https://github.com/boostorg/leaf/issues/46#issuecomment-1169792034  

This is fixed, merged into the master branch, will ship with Boost 1.80.0.

---

## Comment 6

> Username: dnmiller  
> Created at: 2022-07-08 04:15:51 UTC  
> Url: https://github.com/boostorg/leaf/issues/46#issuecomment-1178528737  

> I'm curious about your platform if you don't mind me asking, it's surprising to want BOOST_LEAF_CFG_CAPTURE=0 but also not disable exception handling.  
  
I had actually just included it in an existing project to mess around with and was not paying much attention to the compiler flags...
