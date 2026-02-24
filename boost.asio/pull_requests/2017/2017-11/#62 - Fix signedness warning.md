# #62 Fix signedness warning [Closed]

> Username: berkus  
> Created at: 2017-11-07 10:49:55 UTC  
> Updated at: 2019-02-04 08:34:56 UTC  
> Closed at: 2019-02-04 08:34:56 UTC  
> Url: https://github.com/boostorg/asio/pull/62  

include/boost/asio/ssl/impl/rfc2818_verification.ipp:62:21: warning: comparison of integers of different signs: 'int' and 'size_t' (aka 'unsigned long') [-Wsign-compare]  
  for (int i = 0; i < sk_GENERAL_NAME_num(gens); ++i)  
                  ~ ^ ~~~~~~~~~~~~~~~~~~~~~~~~~

---

## Comment 1

> Username: chriskohlhoff  
> Created_at: 2017-12-02 08:10:14 UTC  
> Url: https://github.com/boostorg/asio/pull/62#issuecomment-348676444  

Please confirm which version of openssl you are using. In every version I have, sk_GENERAL_NAME_num returns an int.

---

## Comment 2

> Username: berkus  
> Created_at: 2017-12-03 17:05:47 UTC  
> Updated_at: 2017-12-03 17:06:51 UTC  
> Url: https://github.com/boostorg/asio/pull/62#issuecomment-348797257  

This is with `boringssl`.  
  
In general this means that all openssl versions have to be patched, I don't think it makes sense to return a negative number of general names.

---
