# #169 - value_builder store pointer to emplace function [Closed]

> Username: vinniefalco  
> Created at: 2020-08-18 19:00:39 UTC  
> Updated at: 2020-08-27 04:33:41 UTC  
> Closed at: 2020-08-27 04:33:41 UTC  
> Url: https://github.com/boostorg/json/issues/169  

Currently the value builder performs a branch to determine whether to call `emplace_array` or `emplace_object`. Instead, this could be stored as a function pointer once when we know we are in an array or object (upon beginning an object or array, or when popping `lev_` from the stack) and used instead of taking a branch.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2020-08-27 04:33:41 UTC  
> Url: https://github.com/boostorg/json/issues/169#issuecomment-681403314  

Obsolete, we don't have these functions anymore
