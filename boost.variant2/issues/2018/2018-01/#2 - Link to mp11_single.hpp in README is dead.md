# #2 - Link to mp11_single.hpp in README is dead [Closed]

> Username: JayFoxRox  
> Created at: 2018-01-04 20:45:35 UTC  
> Updated at: 2019-02-22 17:24:05 UTC  
> Closed at: 2019-02-22 17:24:05 UTC  
> Url: https://github.com/boostorg/variant2/issues/2  

The file is nowhere to be found, and I can't seem to find it in the boost repo either.  
  
See https://github.com/boostorg/mp11/commit/f24d1ca5df53fe9f70ae359a3b8ecd4bb86bcef2#diff-7febf8cf6caa910641c7015450242da9 for the commit which removed it.  
  
Question: Why was it removed?

---

## Comment 1

> Username: pdimov  
> Created at: 2018-01-04 21:25:20 UTC  
> Url: https://github.com/boostorg/variant2/issues/2#issuecomment-355403006  

I removed `mp11_single.hpp` when `mp11` became part of Boost (`mp11` is in Boost 1.66.0). It was too much of a burden to maintain.
