# #87 - Add support for transaction_safe_dynamic as specified by n4514 [Closed]

> Username: badair  
> Created at: 2016-12-23 02:32:40 UTC  
> Updated at: 2017-01-07 06:02:35 UTC  
> Closed at: 2017-01-07 06:02:35 UTC  
> Url: https://github.com/boostorg/callable_traits/issues/87  

http://www.open-std.org/jtc1/sc22/wg21/docs/papers/2015/n4514.pdf  
  
This will be easy to do with the existing design.

---

## Comment 1

> Username: badair  
> Created at: 2017-01-07 06:02:35 UTC  
> Url: https://github.com/boostorg/callable_traits/issues/87#issuecomment-271065599  

Ugh, I went and implemented this fully only to realize that transaction_safe_dynamic is only transaction_safe + an attribute.
