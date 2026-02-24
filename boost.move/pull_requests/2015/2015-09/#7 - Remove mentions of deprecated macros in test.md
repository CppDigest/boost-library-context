# #7 Remove mentions of deprecated macros in test [Merged]

> Username: mclow  
> Created at: 2015-09-11 04:05:04 UTC  
> Updated at: 2015-09-13 19:53:34 UTC  
> Merged at: 2015-09-13 19:51:12 UTC  
> Closed at: 2015-09-13 19:51:12 UTC  
> Url: https://github.com/boostorg/move/pull/7  

Change uses of `BOOST_NO_RVALUE_REFERENCES`, which was deprecated in Boost 1.51.0, to use `BOOST_NO_CXX11_RVALUE_REFERENCES` instead.  
  
No functionality change.

---

## Comment 1

> Username: igaztanaga  
> Created_at: 2015-09-13 19:51:46 UTC  
> Url: https://github.com/boostorg/move/pull/7#issuecomment-139912805  

Thanks Marshall!

---
