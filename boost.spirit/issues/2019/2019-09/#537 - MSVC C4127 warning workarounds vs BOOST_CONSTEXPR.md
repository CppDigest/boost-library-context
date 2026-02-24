# #537 - MSVC C4127 warning workarounds vs BOOST_CONSTEXPR [Closed]

> Username: rubenvb  
> Created at: 2019-09-21 12:34:24 UTC  
> Updated at: 2019-09-30 11:08:35 UTC  
> Closed at: 2019-09-30 11:08:35 UTC  
> Url: https://github.com/boostorg/spirit/issues/537  

There are several places in Spirit that trigger the C4127: conditional expression constant warning.  
  
Essentially these should be `if constexpr` but I see the warning being disabled using pragma's.  
  
I'll make a PR adding a similar pragma push/pop around the occurences I see building my project.  
  
It would better IMHO to add `BOOST_CONTEXPR` after the relevant `if`s, as we can't just rely on `if constexpr` in a C++14 library. I'll make a PR for this too.

---

## Comment 1

> Username: rubenvb  
> Created at: 2019-09-22 08:58:00 UTC  
> Url: https://github.com/boostorg/spirit/issues/537#issuecomment-533862771  

Adding BOOST_CONSTEXPR doesn't work due to C++11/14 not having `if constexpr`, so never mind that. The warning (it was only a single occurence I see in my code) can still be silenced locally.
