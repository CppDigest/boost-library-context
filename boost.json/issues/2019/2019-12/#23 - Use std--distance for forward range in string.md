# #23 - Use std::distance for forward range in string [Closed]

> Username: vinniefalco  
> Created at: 2019-12-29 19:36:40 UTC  
> Updated at: 2020-05-02 02:38:15 UTC  
> Closed at: 2020-05-02 02:38:15 UTC  
> Url: https://github.com/boostorg/json/issues/23  

Algorithms using iterators should use `std::distance` for _ForwardIterator_ or better:  
https://github.com/vinniefalco/json/blob/bc95b55c351248ca10062ad1febdfbf43d11b9b5/include/boost/json/detail/string_impl.hpp#L103

---

## Comment 1

> Username: vinniefalco  
> Created at: 2020-05-02 02:38:15 UTC  
> Url: https://github.com/boostorg/json/issues/23#issuecomment-622657070  

Done
