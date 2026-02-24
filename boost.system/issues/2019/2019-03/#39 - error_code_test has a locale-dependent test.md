# #39 - error_code_test has a locale-dependent test [Closed]

> Username: Kojoley  
> Created at: 2019-03-15 23:47:22 UTC  
> Updated at: 2019-03-16 17:51:36 UTC  
> Closed at: 2019-03-16 17:51:36 UTC  
> Url: https://github.com/boostorg/system/issues/39  

https://github.com/boostorg/system/blob/0134441a6e628f407e77275b675e9a5757f65c0a/test/error_code_test.cpp#L200  
  
It is an only failure I observe in the test file.

---

## Comment 1

> Username: pdimov  
> Created at: 2019-03-16 16:55:45 UTC  
> Url: https://github.com/boostorg/system/issues/39#issuecomment-473564770  

There is a check for English here:  
  
https://github.com/boostorg/system/blob/0134441a6e628f407e77275b675e9a5757f65c0a/test/system_error_test.cpp#L38-L46  
  
but not there, I'm not sure why. I'll look into it.
