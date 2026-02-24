# #30 Remove use of deprecated macros; use replacements [Merged]

> Username: mclow  
> Created at: 2015-09-10 20:29:05 UTC  
> Updated at: 2015-09-10 22:36:13 UTC  
> Merged at: 2015-09-10 20:39:43 UTC  
> Closed at: 2015-09-10 20:39:43 UTC  
> Url: https://github.com/boostorg/container/pull/30  

The macro `BOOST_NO_RVALUE_REFERENCES` was deprecated in boost 1.51.0, and was replaced by `BOOST_NO_CXX11_RVALUE_REFERENCES`.  It's been 8 releases since these were deprecated, and I'd like to remove them  
  
Replace all uses of `BOOST_NO_RVALUE_REFERENCES` with `BOOST_NO_CXX11_RVALUE_REFERENCES`.   
  
No functionality change.

---

## Comment 1

> Username: igaztanaga  
> Created_at: 2015-09-10 20:39:58 UTC  
> Url: https://github.com/boostorg/container/pull/30#issuecomment-139372692  

Thanks !

---
