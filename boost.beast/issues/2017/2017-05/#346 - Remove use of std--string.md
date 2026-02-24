# #346 - Remove use of std::string [Closed]

> Username: vinniefalco  
> Created at: 2017-05-08 00:53:53 UTC  
> Updated at: 2017-06-21 01:20:39 UTC  
> Closed at: 2017-06-21 01:20:38 UTC  
> Url: https://github.com/boostorg/beast/issues/346  

Most uses of `std::string` can now use `static_string` and eliminate the need for heap allocation.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-06-21 01:20:38 UTC  
> Url: https://github.com/boostorg/beast/issues/346#issuecomment-309935527  

Done
