# #117 - Better Reader [Closed]

> Username: vinniefalco  
> Created at: 2016-10-09 23:39:20 UTC  
> Updated at: 2016-10-20 22:00:10 UTC  
> Closed at: 2016-10-20 22:00:10 UTC  
> Url: https://github.com/boostorg/beast/issues/117  

The Reader concept should assure that the full headers are available when the reader is constructed, to permit optimizations such as reserving memory. e.g.:  
https://github.com/vinniefalco/Beast/blob/message/include/beast/http/string_body.hpp#L23

---

## Comment 1

> Username: vinniefalco  
> Created at: 2016-10-14 23:20:19 UTC  
> Url: https://github.com/boostorg/beast/issues/117#issuecomment-253942277  

Also we should add `void init(error_code& ec)` to the requirements.
