# #3 Add dependency for boost::coroutine and boost::fiber on boost::context library [Merged]

> Username: volo-zyko  
> Created at: 2018-02-22 15:21:51 UTC  
> Updated at: 2019-10-31 19:28:09 UTC  
> Merged at: 2019-10-31 19:28:09 UTC  
> Closed at: 2019-10-31 19:28:09 UTC  
> Url: https://github.com/boostorg/bcp/pull/3  

Without these lines, copied `coroutine` or `fiber` libraries fail to build as they depend on `segmented-stacks` feature which is defined in context library.

---
