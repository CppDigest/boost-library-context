# #1282 - Test libraries and executables should be declared EXCLUDE_FROM_ALL [Closed]

> Username: pdimov  
> Created at: 2024-05-14 18:08:30 UTC  
> Updated at: 2024-09-13 13:03:16 UTC  
> Closed at: 2024-09-13 13:03:16 UTC  
> Url: https://github.com/boostorg/geometry/issues/1282  

The Boost convention is that `cmake --build .` only builds the libraries. Test libraries and executables are built with `cmake --build . --target tests`.

---

## Comment 1

> Username: barendgehrels  
> Created at: 2024-09-11 06:50:39 UTC  
> Url: https://github.com/boostorg/geometry/issues/1282#issuecomment-2342802996  

@vissarion I think this is handled now by one of your last PR's?

---

## Comment 2

> Username: vissarion  
> Created at: 2024-09-11 10:17:50 UTC  
> Url: https://github.com/boostorg/geometry/issues/1282#issuecomment-2343236092  

> @vissarion I think this is handled now by one of your last PR's?  
  
Yes, https://github.com/boostorg/geometry/pull/1302 which is waiting a review feedback. Upon merging this issue will be closed automatically.
