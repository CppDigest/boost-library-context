# #506 - Clean up the teardown tag [Closed]

> Username: vinniefalco  
> Created at: 2017-06-18 03:01:04 UTC  
> Updated at: 2017-06-21 00:27:21 UTC  
> Closed at: 2017-06-21 00:27:21 UTC  
> Url: https://github.com/boostorg/beast/issues/506  

The business of using helpers to get ADL to kick was only for legacy compilers, we can do away with the whole `call_teardown` in the helper namespace and just invoke it directly. The tag is not needed.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-06-21 00:27:21 UTC  
> Url: https://github.com/boostorg/beast/issues/506#issuecomment-309928405  

No can do, because ADL won't find the overloads.
