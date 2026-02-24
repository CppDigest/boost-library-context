# #486 - How does pool handles transactions ? [Closed]

> Username: gitGameStory  
> Created at: 2025-06-18 15:09:36 UTC  
> Updated at: 2025-06-19 17:45:15 UTC  
> Closed at: 2025-06-19 17:45:15 UTC  
> Url: https://github.com/boostorg/mysql/issues/486  

Hi!   
Thanx for amazing lib!  
  
I have 2 questions  
  
1. May i reuse mysql::results result and mysql::statement variables ?  
  
2. How errors in transactions are handled in the pool. Are transactions automatically rollbacked or i should rollback manually in catch section ?

---

## Comment 1

> Username: anarthal  
> Created at: 2025-06-18 15:25:49 UTC  
> Url: https://github.com/boostorg/mysql/issues/486#issuecomment-2984683486  

Hi @gitGameStory,  
  
Thank you for using the lib :)  
  
> 1. May i reuse mysql::results result and mysql::statement variables ?  
  
You might re-use `mysql::results` as you see fit. But bear in mind that this is not a thread-safe class. Think of it as if you were re-using a `std::vector` - it will save memory allocations, but it's on you to ensure safety.  
  
`mysql::statement` is non-allocating (just a couple of ints identifying the server-side handle), so re-using the variable itself makes no sense. You can re-use statements (as in "prepare a statement once, then execute it multiple times"), but it's not straightforward to do with the pool at the moment. More in this in the next section.  
  
> 2. How errors in transactions are handled in the pool. Are transactions automatically rollbacked or i should rollback manually in catch section ?  
  
In MySQL, if you start a transaction with `START TRANSACTION`, but you don't commit it, and then you either close the connection or call `async_reset_connection`, the transaction is automatically rolled back as part of session cleanup. When a connection is returned to the pool (that is, when a `pooled_connection` object gets destroyed), the pool will execute an `async_reset_connection` in the background before handing the connection back to you. This will roll back automatically any open transaction. You can opt out from this behavior by calling `pooled_connection::return_without_reset()`, which returns the connection but doesn't schedule a reset.  
  
`async_reset_connection` will also deallocate any statements prepared by `async_prepare_statement`. This means that any statement that you prepare with a `pooled_connection` will be deallocated when the connection gets returned to the pool, unless you call `return_without_reset`.  
  
As an alternative to statements, you might consider `with_params`, which doesn't require preparing statements upfront.  
  
Now, depending on your use case, I see two viable options:  
  
1. You call `async_get_connection`, then prepare a statement, then execute it. You don't call `return_without_reset`, so statements get closed when the connection is returned to the pool.  
2. You call `async_get_connection`, then execute your query with `with_params`. You call `return_without_reset` only if everything went well (no cleanup required).  
  
In any case, you don't need any try/catch statements to roll back transactions. It will just happen for you.  
  
Note: 2 is likely much more efficient than 1 unless your queries return big amounts of data.  
  
If you tell me more about your use case, I can probably help more.  
  
Note that statement caching has been brought up before in #252, but I've never come to implement it because `with_params` seemed to fill the gap. Please let me know if this is a feature you'd like to see.  
  
Regards,  
Ruben.

---

## Comment 2

> Username: gitGameStory  
> Created at: 2025-06-18 15:51:41 UTC  
> Url: https://github.com/boostorg/mysql/issues/486#issuecomment-2984795311  

Thanx for detailed answer!

---

## Comment 3

> Username: anarthal  
> Created at: 2025-06-19 17:45:15 UTC  
> Url: https://github.com/boostorg/mysql/issues/486#issuecomment-2988793827  

I will close this issue then. Feel free to open another one if you need any more help.
