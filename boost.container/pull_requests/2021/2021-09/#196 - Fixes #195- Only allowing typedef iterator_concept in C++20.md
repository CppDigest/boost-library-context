# #196 Fixes #195: Only allowing typedef iterator_concept in C++20 [Closed]

> Username: TristanFloch  
> Created at: 2021-09-30 11:37:34 UTC  
> Updated at: 2022-01-04 15:09:40 UTC  
> Closed at: 2022-01-04 09:35:41 UTC  
> Url: https://github.com/boostorg/container/pull/196  

Fixes #195.  
  
I was a bit surprised that there were no better way to check if the standard was C++20. Please correct me if there is.

---

## Comment 1

> Username: theodelrieu  
> Created_at: 2022-01-03 12:53:02 UTC  
> Url: https://github.com/boostorg/container/pull/196#issuecomment-1004070923  

Any news on this?

---

## Comment 2

> Username: TristanFloch  
> Created_at: 2022-01-03 12:58:25 UTC  
> Url: https://github.com/boostorg/container/pull/196#issuecomment-1004073496  

> Any news on this?  
  
None yet.

---

## Comment 3

> Username: pdimov  
> Created_at: 2022-01-03 18:25:30 UTC  
> Url: https://github.com/boostorg/container/pull/196#issuecomment-1004273548  

The relatively new `BOOST_CXX_VERSION` is what one uses nowadays, if no appropriate Config or feature macro exists.

---

## Comment 4

> Username: pdimov  
> Created_at: 2022-01-03 18:29:23 UTC  
> Url: https://github.com/boostorg/container/pull/196#issuecomment-1004275943  

In this case, the feature test macro that controls whether `<iterator>` has the new tags and `iterator_concept` seems to be `__cpp_lib_ranges`. (`<iterator>` must have been included beforehand.)

---

## Comment 5

> Username: igaztanaga  
> Created_at: 2022-01-04 11:01:07 UTC  
> Url: https://github.com/boostorg/container/pull/196#issuecomment-1004712912  

> In this case, the feature test macro that controls whether `<iterator>` has the new tags and `iterator_concept` seems to be `__cpp_lib_ranges`. (`<iterator>` must have been included beforehand.)  
  
In theory, for C++20 features we could also check if <version> header exists and if so, check for __cpp_lib_ranges, I guess.

---

## Comment 6

> Username: pdimov  
> Created_at: 2022-01-04 15:09:40 UTC  
> Url: https://github.com/boostorg/container/pull/196#issuecomment-1004891610  

Checking for header existence is pretty useless in practice, as the header might exist but be empty or generate an `#error`. Fortunately, there's no need for that here, because `<iterator>` always exists.

---
