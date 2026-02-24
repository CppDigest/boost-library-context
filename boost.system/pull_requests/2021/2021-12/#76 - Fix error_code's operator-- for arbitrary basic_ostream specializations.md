# #76 Fix error_code's operator<< for arbitrary basic_ostream specializations [Merged]

> Username: MarcelRaad  
> Created at: 2021-12-21 12:21:18 UTC  
> Updated at: 2021-12-22 08:16:20 UTC  
> Merged at: 2021-12-21 20:32:12 UTC  
> Closed at: 2021-12-21 20:32:12 UTC  
> Url: https://github.com/boostorg/system/pull/76  

This fixes a regression from commit  
https://github.com/boostorg/system/commit/a9b64a888a24400cc2af9910a6ff88c3c4fd3210.  
Calling `operator<<` with `std::string` only works for `std::ostream`.  
Use `c_str()` to restore the previous behavior of using `const char*`,  
which works for any `basic_ostream` specialization.

---

## Comment 1

> Username: pdimov  
> Created_at: 2021-12-21 16:27:37 UTC  
> Url: https://github.com/boostorg/system/pull/76#issuecomment-998920539  

Do you have a code example that demonstrates the failure?

---

## Comment 2

> Username: pdimov  
> Created_at: 2021-12-21 16:36:52 UTC  
> Url: https://github.com/boostorg/system/pull/76#issuecomment-998927003  

This one probably. https://godbolt.org/z/soPKj3zWc

---

## Comment 3

> Username: MarcelRaad  
> Created_at: 2021-12-21 17:12:53 UTC  
> Updated_at: 2021-12-21 17:15:29 UTC  
> Url: https://github.com/boostorg/system/pull/76#issuecomment-998953197  

Yes, that's a good one. And I'm using MSVC 19.29.

---

## Comment 4

> Username: MarcelRaad  
> Created_at: 2021-12-22 08:16:20 UTC  
> Url: https://github.com/boostorg/system/pull/76#issuecomment-999372351  

Thanks for merging and adding the regression test!

---
