# #775 - has_lowest_layer trait [Closed]

> Username: vinniefalco  
> Created at: 2017-09-09 23:39:25 UTC  
> Updated at: 2019-02-23 05:45:20 UTC  
> Closed at: 2019-02-23 05:45:20 UTC  
> Url: https://github.com/boostorg/beast/issues/775  

We need the `has_lowest_layer` trait to fix a problem with `stream::lowest_layer()`. Currently the implementation calls `stream_.lowest_layer()` which is incorrect when the stream does not have the function. Instead, the implementation should return `stream_` if there is no `stream_.lowest_layer()`, otherwise return `stream_.lowest_layer()`.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2019-02-23 05:45:20 UTC  
> Url: https://github.com/boostorg/beast/issues/775#issuecomment-466619647  

This is now solved a different way, we no longer need the `lowest_layer_type` or `lowest_layer` members at all, we just need a `next_layer`, and `beast::get_lowest_layer(s)` Just Works.
