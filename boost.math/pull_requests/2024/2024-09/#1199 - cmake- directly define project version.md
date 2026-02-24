# #1199 cmake: directly define project version [Merged]

> Username: lucascolley  
> Created at: 2024-09-15 19:58:20 UTC  
> Updated at: 2024-09-16 13:26:52 UTC  
> Merged at: 2024-09-16 12:55:10 UTC  
> Closed at: 2024-09-16 12:55:10 UTC  
> Url: https://github.com/boostorg/math/pull/1199  

See explanation and discussion at https://lists.boost.org/Archives/boost/2024/09/257793.php. cc @pdimov, x-ref https://github.com/scipy/scipy/pull/21270

---

## Comment 1

> Username: mborland  
> Created_at: 2024-09-16 12:31:20 UTC  
> Url: https://github.com/boostorg/math/pull/1199#issuecomment-2352786902  

These changes look fine to me. The real question is will this be better for you? About 2 years ago when Scipy moved to using the Boost.Math standalone submodule (https://github.com/scipy/scipy/pull/17432) you were able to start tracking the head commit of math instead of having to wait for the tri-annual release. Won't this put you back in a position where you are waiting potentially months for fixes or features? Example. I am pretty sure your implementation of the Landau distribution relies on ours right now (through `_ufuncs`), but the Landau distribution has not made any official release for us yet and will not until 11 December at the earliest.

---

## Comment 2

> Username: lucascolley  
> Created_at: 2024-09-16 12:40:47 UTC  
> Url: https://github.com/boostorg/math/pull/1199#issuecomment-2352808399  

> The real question is will this be better for you? About 2 years ago when Scipy moved to using the Boost.Math standalone submodule (scipy/scipy#17432) you were able to start tracking the head commit of math instead of having to wait for the tri-annual release. Won't this put you back in a position where you are waiting potentially months for fixes or features?  
  
Indeed, there is a trade-off to be made there. We haven't made that decision yet, but these changes will enable us to go either way.

---

## Comment 3

> Username: mborland  
> Created_at: 2024-09-16 12:54:46 UTC  
> Url: https://github.com/boostorg/math/pull/1199#issuecomment-2352840907  

> Indeed, there is a trade-off to be made there. We haven't made that decision yet, but these changes will enable us to go either way.  
  
Sounds good. Since this adds a maintenance burden of like 30 seconds every 4 months I am not opposed.

---

## Comment 4

> Username: lucascolley  
> Created_at: 2024-09-16 13:26:51 UTC  
> Url: https://github.com/boostorg/math/pull/1199#issuecomment-2352920858  

Thanks!

---
