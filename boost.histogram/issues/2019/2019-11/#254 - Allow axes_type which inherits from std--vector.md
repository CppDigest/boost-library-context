# #254 - Allow axes_type which inherits from std::vector<...> [Closed]

> Username: HDembinski  
> Created at: 2019-11-23 23:26:10 UTC  
> Updated at: 2020-05-20 00:50:47 UTC  
> Closed at: 2020-05-20 00:50:47 UTC  
> Url: https://github.com/boostorg/histogram/issues/254  

Currently, users cannot customize the two possible axes types based on std::tuple and std::vector. It would be nice to at least support types inherited from `std::vector<axis::variant<...>>`, because this allows one to hide the large type name from compiler error messages and in signatures.

---

## Comment 1

> Username: HDembinski  
> Created at: 2020-05-20 00:50:47 UTC  
> Url: https://github.com/boostorg/histogram/issues/254#issuecomment-631168659  

conclusion after more thought: this does not work
