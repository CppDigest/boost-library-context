# #150 - fail_counter tests should check the results on success [Closed]

> Username: vinniefalco  
> Created at: 2016-10-20 04:32:21 UTC  
> Updated at: 2017-09-01 03:03:39 UTC  
> Closed at: 2017-09-01 03:03:38 UTC  
> Url: https://github.com/boostorg/beast/issues/150  

On success, tests using `fail_counter` (like `http::read`) should convert the parsed output back to a string and make sure that it matches the input.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-09-01 03:03:38 UTC  
> Url: https://github.com/boostorg/beast/issues/150#issuecomment-326475641  

Cross-cutting concern
