# #339 - Refactor is_ and if_ interfaces [Closed]

> Username: vinniefalco  
> Created at: 2020-09-14 23:42:55 UTC  
> Updated at: 2020-09-16 01:59:48 UTC  
> Closed at: 2020-09-16 01:59:48 UTC  
> Url: https://github.com/boostorg/json/issues/339  

We made a change so that `is_object` and other is_ functions return a pointer to the represented value. This needs to be rolled back to using `if_object`, `if_array`, etc.. where the `is_` functions return bool again. This means that `contains` on array and string should return `bool`, and we need to add `if_contains` which returns a pointer.
