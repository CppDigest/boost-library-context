# #714 - Remove unnecessary header [Closed]

> Username: alandefreitas  
> Created at: 2022-06-08 17:12:35 UTC  
> Updated at: 2022-06-09 14:52:19 UTC  
> Closed at: 2022-06-09 14:52:19 UTC  
> Url: https://github.com/boostorg/json/issues/714  

The header `boost/exception/diagnostic_information.hpp` is unnecessary. It increases the library weight in the boost dependency report.  
  
https://github.com/boostorg/json/blob/4797be3eac137729679403b89bd4f8580797737b/include/boost/json/detail/except.hpp#L15  
  
### Version of Boost  
  
develop  
  
### Steps necessary to reproduce the problem  
  
Use the library as usual. Not really a bug.  
  
### All relevant compiler information  
  
Any compiler. Not really a bug.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2022-06-08 22:04:55 UTC  
> Url: https://github.com/boostorg/json/issues/714#issuecomment-1150461918  

Why is this an issue instead of a pull request?

---

## Comment 2

> Username: alandefreitas  
> Created at: 2022-06-08 23:57:52 UTC  
> Url: https://github.com/boostorg/json/issues/714#issuecomment-1150527747  

> Why is this an issue instead of a pull request?  
  
:)  
  
Because I'm looking at boost.url right now but didn't want to forget about this.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2022-06-08 23:59:01 UTC  
> Url: https://github.com/boostorg/json/issues/714#issuecomment-1150528257  

Oh.. and its in JSON... derp

---

## Comment 4

> Username: alandefreitas  
> Created at: 2022-06-09 00:00:18 UTC  
> Url: https://github.com/boostorg/json/issues/714#issuecomment-1150528747  

Yes. I already fixed this in boost.url.
