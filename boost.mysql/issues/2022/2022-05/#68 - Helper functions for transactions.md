# #68 - Helper functions for transactions [Closed]

> Username: anarthal  
> Created at: 2022-05-12 10:53:23 UTC  
> Updated at: 2024-08-10 16:14:17 UTC  
> Closed at: 2024-08-10 16:14:17 UTC  
> Url: https://github.com/boostorg/mysql/issues/68  

Provide helper functions `connection::start_transaction`, `connection::commit` and `connection::rollback` (and their async counterparts).

---

## Comment 1

> Username: anarthal  
> Created at: 2024-08-10 16:14:17 UTC  
> Url: https://github.com/boostorg/mysql/issues/68#issuecomment-2282197864  

This would imply 4 more overloads for each method, when it's trivial to issue the commands manually. I don't think it's worth it at all. We also have examples demonstrating transactions.
