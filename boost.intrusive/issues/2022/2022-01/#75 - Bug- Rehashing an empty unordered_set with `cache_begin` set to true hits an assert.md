# #75 - Bug: Rehashing an empty unordered_set with `cache_begin` set to true hits an assert [Closed]

> Username: seelabs  
> Created at: 2022-01-17 22:32:27 UTC  
> Updated at: 2022-06-25 16:36:30 UTC  
> Closed at: 2022-06-25 16:36:30 UTC  
> Url: https://github.com/boostorg/intrusive/issues/75  

Creating a `boost::intrusive::make_unordered_set` with `cache_begin` set to true and immediately rehashing will hit an assert. The assert that's hit is here: https://github.com/boostorg/intrusive/blob/5c4d116b82f2c3488e091a7c2e37a486197dcf86/include/boost/intrusive/hashtable.hpp#L1264  
  
If `cache_begin` is set to false, or the collection is not empty, the assert is not hit.  
  
Here is a gist of a small program that will reproduce the issue: https://gist.github.com/seelabs/c91655d1a3516f854b17145faac7db25

---

## Comment 1

> Username: igaztanaga  
> Created at: 2022-06-25 16:33:46 UTC  
> Url: https://github.com/boostorg/intrusive/issues/75#issuecomment-1166320963  

Many thanks for the report and the test case. And sorry for the delay replying.
