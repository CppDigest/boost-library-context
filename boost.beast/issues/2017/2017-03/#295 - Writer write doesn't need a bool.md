# #295 - Writer write doesn't need a bool [Closed]

> Username: vinniefalco  
> Created at: 2017-03-31 20:31:38 UTC  
> Updated at: 2017-06-09 00:44:16 UTC  
> Closed at: 2017-06-09 00:44:16 UTC  
> Url: https://github.com/boostorg/beast/issues/295  

**Writer** `write` function could have no return value and specify that if the write function is not called, it indicates the end of the body data.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-06-09 00:44:16 UTC  
> Url: https://github.com/boostorg/beast/issues/295#issuecomment-307264936  

This whole thing got revamped and its good now.
