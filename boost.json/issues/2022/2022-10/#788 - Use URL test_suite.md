# #788 - Use URL test_suite [Open]

> Username: vinniefalco  
> Created at: 2022-10-26 16:40:59 UTC  
> Updated at: 2024-10-09 14:24:55 UTC  
> Url: https://github.com/boostorg/json/issues/788  

Now that Boost.URL is in the superproject we can use its testing framework and eliminate the older, redundant copy of the one in JSON:  
  
https://github.com/boostorg/url/tree/develop/extra

---

## Comment 1

> Username: grisumbras  
> Created at: 2024-10-09 14:24:54 UTC  
> Url: https://github.com/boostorg/json/issues/788#issuecomment-2402497680  

From #830  
  
> Now that Boost.URL is accepted, Boost.JSON can throw out its older iteration of test_suite.hpp and use the one from URL, which is updated to be almost identical in behavior to Boost lightweight test in terms of diagnostic outputs.  
>  
> For integration tips look to Boost.Http.Proto:  
https://github.com/CPPAlliance/http_proto/blob/f795e313bee75ca4310f899375227c23f64e1909/test/unit/Jamfile#L12 and https://github.com/CPPAlliance/http_proto/blob/f795e313bee75ca4310f899375227c23f64e1909/test/unit/CMakeLists.txt#L10 .
