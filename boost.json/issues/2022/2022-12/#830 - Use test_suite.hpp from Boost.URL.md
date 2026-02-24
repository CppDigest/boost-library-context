# #830 - Use test_suite.hpp from Boost.URL [Closed]

> Username: vinniefalco  
> Created at: 2022-12-28 15:51:48 UTC  
> Updated at: 2024-10-09 14:25:21 UTC  
> Closed at: 2024-10-09 14:25:21 UTC  
> Url: https://github.com/boostorg/json/issues/830  

Now that Boost.URL is accepted, Boost.JSON can throw out its older iteration of test_suite.hpp and use the one from URL, which is updated to be almost identical in behavior to Boost lightweight test in terms of diagnostic outputs.  
  
For integration tips look to Boost.Http.Proto:  
https://github.com/CPPAlliance/http_proto/blob/f795e313bee75ca4310f899375227c23f64e1909/test/unit/Jamfile#L12 and https://github.com/CPPAlliance/http_proto/blob/f795e313bee75ca4310f899375227c23f64e1909/test/unit/CMakeLists.txt#L10 .

---

## Comment 1

> Username: grisumbras  
> Created at: 2024-10-09 14:25:21 UTC  
> Url: https://github.com/boostorg/json/issues/830#issuecomment-2402498906  

Closing as duplicate of #788.
