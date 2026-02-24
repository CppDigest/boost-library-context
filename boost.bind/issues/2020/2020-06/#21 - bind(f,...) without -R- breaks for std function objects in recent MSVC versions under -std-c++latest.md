# #21 - bind(f,...) without <R> breaks for std function objects in recent MSVC versions under /std:c++latest [Closed]

> Username: joaquintides  
> Created at: 2020-06-29 10:08:48 UTC  
> Updated at: 2021-03-06 21:14:01 UTC  
> Closed at: 2021-03-06 21:14:01 UTC  
> Url: https://github.com/boostorg/bind/issues/21  

Starting (at least) in MSVC 19.20, `/std:c++latest` results in internal `_HAS_CXX20` macro being defined, which effectively removes `result_type` nested typedef from std library function objects such as `std::equal_to` etc. This breaks code using `boost::bind` without explicit `R` specification.

---

## Comment 1

> Username: pdimov  
> Created at: 2020-06-29 11:31:30 UTC  
> Url: https://github.com/boostorg/bind/issues/21#issuecomment-651054510  

Wonderful.

---

## Comment 2

> Username: pdimov  
> Created at: 2020-06-30 00:55:38 UTC  
> Url: https://github.com/boostorg/bind/issues/21#issuecomment-651451164  

This seems a more recent change; Godbolt's 19.20-19.24 still have `result_type`, and so does Appveyor. My local 19.26, however, no longer has it.

---

## Comment 3

> Username: joaquintides  
> Created at: 2020-06-30 07:10:31 UTC  
> Url: https://github.com/boostorg/bind/issues/21#issuecomment-651593904  

According to VS changelog, the removal happened in [VS 2019 16.6](https://github.com/microsoft/STL/wiki/Changelog#vs-2019-166) (look for P0619R4), which is 19.26, I think.

---

## Comment 4

> Username: pdimov  
> Created at: 2021-03-06 21:14:01 UTC  
> Url: https://github.com/boostorg/bind/issues/21#issuecomment-792055626  

I believe this has been fixed with https://github.com/boostorg/bind/commit/b601e8924d9d86e18a02fec5418e9696678fb8a8.
