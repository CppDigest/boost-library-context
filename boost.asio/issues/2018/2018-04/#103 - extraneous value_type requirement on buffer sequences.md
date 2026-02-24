# #103 - extraneous value_type requirement on buffer sequences [Closed]

> Username: vinniefalco  
> Created at: 2018-04-28 01:28:12 UTC  
> Updated at: 2020-12-30 00:50:46 UTC  
> Closed at: 2020-12-30 00:50:45 UTC  
> Url: https://github.com/boostorg/asio/issues/103  

The implementation of buffer sequence type checks requires the nested `value_type`, but the documented requirements for buffer sequences omits this requirement. This should be resolved by removing the requirement for a nested `value_type` in the metafunction.

---

## Comment 1

> Username: ghost  
> Created at: 2020-12-30 00:50:44 UTC  
> Url: https://github.com/boostorg/asio/issues/103#issuecomment-752289401  

This issue was moved by [chriskohlhoff](https://github.com/chriskohlhoff) to [chriskohlhoff/asio#610](https://github.com/chriskohlhoff/asio/issues/610).
