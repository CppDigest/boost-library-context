# #120 - Better callback traits in basic_parser_v1 [Closed]

> Username: vinniefalco  
> Created at: 2016-10-10 11:40:46 UTC  
> Updated at: 2016-10-14 21:51:28 UTC  
> Closed at: 2016-10-14 21:51:28 UTC  
> Url: https://github.com/boostorg/beast/issues/120  

`basic_parser_v1` traits check for a function signature with an exact match. If a programmer gets a parameter type wrong, the program will still compile but fail to behave correctly. The traits should be relaxed to only check that a member with the given name exists. Then, in the invoking function, a `static_assert` can check that the argument types are correct, giving better diagnostics.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2016-10-14 21:51:28 UTC  
> Url: https://github.com/boostorg/beast/issues/120#issuecomment-253928441  

Merged in b16
