# #101 Construct the unqualified value_type with placement new [Merged]

> Username: pdimov  
> Created at: 2022-02-18 20:06:08 UTC  
> Updated at: 2022-03-01 19:07:44 UTC  
> Merged at: 2022-03-01 19:07:44 UTC  
> Closed at: 2022-03-01 19:07:44 UTC  
> Url: https://github.com/boostorg/optional/pull/101  

The standard says in https://eel.is/c++draft/basic.memobj#basic.life-8 that we're allowed to reconstruct an object of the same type in the storage occupied by an earlier one, and old pointers and references will refer to the new object, as long as certain requirements are met, one of which is that the old object wasn't `const`.  
  
To avoid problems with this requirement in `optional<const X>` uses, this PR changes all the placement `new` statements to construct an object of `remove_const<T>::type` instead.  
  
(For context, please see thread starting at https://lists.boost.org/Archives/boost/2022/02/252503.php.)

---
