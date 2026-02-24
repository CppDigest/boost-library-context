# #336 - How to properly rollback a transaction when an exception is thrown? [Closed]

> Username: sigasigasiga  
> Created at: 2024-08-18 21:58:37 UTC  
> Updated at: 2024-08-21 08:42:34 UTC  
> Closed at: 2024-08-21 08:42:34 UTC  
> Url: https://github.com/boostorg/mysql/issues/336  

Examples in the documentation ([link](https://live.boost.org/doc/libs/1_86_0/libs/mysql/doc/html/mysql/examples/prepared_statements_cpp11.html)) don't answer that question, although I think it is pretty important.  
  
I was thinking about implementing some sort of `scoped_transaction` class that'd be defined like this:  
```cpp  
// not a real code, just a mock to give you the idea  
struct scoped_transaction  
{  
  boost::mysql::any_connection &conn_;  
  int exceptions_ = std::uncaught_exceptions();  
  ~scoped_transaction() {  
    if(std::uncaught_exceptions() == exceptions_) conn_.execute("COMMIT");  
    else conn_.execute("ROLLBACK");  
  }  
};  
```  
  
but since `{,async_}execute` methods are not marked as `noexcept` I thought that this wouldn't be a great idea. Also, implementing it using coroutines is also not an easy task.  
  
Does the library provide any utilities to overcome this problem?

---

## Comment 1

> Username: anarthal  
> Created at: 2024-08-19 07:59:24 UTC  
> Url: https://github.com/boostorg/mysql/issues/336#issuecomment-2295912068  

Depending on what you're trying to achieve, you have different options. In any case, the key point is that MySQL will roll back any open transaction automatically when the session that started them is either closed or reset. That means that the following actions will cause a transaction rollback:  
- Successfully calling `(async_)execute("ROLLBACK")` as you're suggesting.  
- Successfully calling `(async_)reset_connection()` (although please read this function's docs before using it, as it has some non-obvious behavior server-side).  
- Calling `(async_)close()`.  
- Closing the underlying socket, which happens when an `any_connection` object goes out of scope.  
- If you're using connection pools, returning a connection to the pool also rolls back any open transactions.  
  
Now, I'd say there are two possible scenarios for you:  
- If you're opening a new session for each request involving a transaction, just close the connection on error, and that will roll back failed transactions. Just call commit after everything you're doing worked fine.  
- If you're re-using sessions, I'd advise to use `connection_pool`, and follow a similar scheme. Just call `COMMIT` if everything worked fine, and let `pooled_connection` destructor handle it.  
  
Under the hood, returning a connection to the pool will mark it as "pending reset". The implementation will attempt a background `async_reset_connection` + `async_set_character_set`, which rolls back open transactions.  
  
Note that `execute` is not marked as `noexcept` because it involves communication with the server, and thus may block and fail. For instance, if the exception happened because you lost communication with the server, executing the rollback will fail (it won't be required in any case). There isn't a built-in async equivalent for the RAII pattern. I know Boost.Cobalt implemented the equivalent of Python's [async with](https://www.boost.org/doc/libs/master/libs/cobalt/doc/html/index.html#with), but again, it can fail on error.  
  
If this answer doesn't fulfill your needs, please expand on your use case and I'll try to expand on my advice.  
  
Regards,  
Ruben.

---

## Comment 2

> Username: sigasigasiga  
> Created at: 2024-08-19 21:18:53 UTC  
> Url: https://github.com/boostorg/mysql/issues/336#issuecomment-2297475381  

Thank you so much for such a detailed answer!  
  
I'm writing some sort of library based on Boost.MySQL and I was wondering if it is possible to offer a strong exception guarantee, but I guess that only a basic one is possible

---

## Comment 3

> Username: anarthal  
> Created at: 2024-08-21 08:42:34 UTC  
> Url: https://github.com/boostorg/mysql/issues/336#issuecomment-2301494664  

Yep. I don't think offering a strong guarantee is possible if the cleanup action requires a network transfer.  
  
I'll close this issue now. Please feel free to ask any other questions you may have.  
  
Regards,  
Ruben.
