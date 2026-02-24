# #157 - to_std_string example program [Closed]

> Username: vinniefalco  
> Created at: 2020-08-13 20:29:07 UTC  
> Updated at: 2020-10-16 18:51:23 UTC  
> Closed at: 2020-10-16 18:51:23 UTC  
> Url: https://github.com/boostorg/json/issues/157  

`to_std_string` should not be a public API, but we can still give users the function as part of a compiling example.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2020-10-16 18:51:23 UTC  
> Url: https://github.com/boostorg/json/issues/157#issuecomment-710420758  

Actually, now we _only_ convert to `std::string`, because it is unambiguous that the result is a serialized string.
