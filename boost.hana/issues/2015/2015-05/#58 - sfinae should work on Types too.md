# #58 - [Maybe] sfinae should work on Types too [Closed]

> Username: ldionne  
> Created at: 2015-05-01 18:48:34 UTC  
> Updated at: 2015-09-15 21:28:46 UTC  
> Closed at: 2015-06-02 01:14:38 UTC  
> Url: https://github.com/boostorg/hana/issues/58  

Basically, I want a neat way of checking whether an expression is valid, given only types. It should also be possible to check whether the expression is valid without actually computing it when it is valid.

---

## Comment 1

> Username: ldionne  
> Created at: 2015-06-01 15:58:13 UTC  
> Url: https://github.com/boostorg/hana/issues/58#issuecomment-107605784  

`sfinae` can't work on `Type`s, because it needs to return the result of the computation, which requires the actual values. However, 3568186dacd8f859e5b6f3e1719f6aa517c30598 introduces `is_valid`, which almost does what's needed here. The current problem with `is_valid` is that it works on both `Type`s and values, but coerces `Type`s into values with `declval`. This makes it very difficult to check whether some type `T` has a nested `T::member_type`. Will close this issue when I fix `is_valid` so that it becomes possible to check expressions involving only types.  
  
This is somewhat related to #73.
