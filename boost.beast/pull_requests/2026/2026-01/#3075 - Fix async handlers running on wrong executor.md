# #3075 Fix async handlers running on wrong executor [Open]

> Username: ssam18  
> Created at: 2026-01-24 02:00:10 UTC  
> Updated at: 2026-02-02 13:48:21 UTC  
> Url: https://github.com/boostorg/beast/pull/3075  

The async_base::complete function now correctly binds the dispatched handler to the operation's executor, thereby ensuring the appropriate execution context.

---

## Comment 1

> Username: sehe  
> Created_at: 2026-01-24 02:22:44 UTC  
> Updated_at: 2026-01-24 02:23:58 UTC  
> Url: https://github.com/boostorg/beast/pull/3075#issuecomment-3793518223  

The fix (also?) doesn't look accurate to me.  
  
In particular, you applied it to the branch where the documentation claims the immediate executor *should* be used, however, your bind overrides it with any associated *non-immediate* executor.  
  
Note that `this->get_immediate_executor()` correctly invokes `boost::asio::get_associated_immediate_executor` on the underlying handler `h_`.  
  
Assuming that you ran into this as a problem in your code, can you share a minimal example where the wrong executor is used? Then we can understand what actually needs to be fixed. I can see two potential causes here:  
  
 - the doxygen comment is wrong regarding the use of immediate executor  
 - the fallback used in https://github.com/boostorg/beast/pull/3075/files#diff-83960c537c353ffc6a1431f079ce863a30ef12d2943b43437aecddcb3c8b7e80R336 is inaccurate? (Might need to be `this->get_executor()` over `wg1_.get_executor()`?)  
  
That is, assuming you have demonstrated a case where the wrong executor is effectively used.

---

## Comment 2

> Username: ashtum  
> Created_at: 2026-02-01 15:22:23 UTC  
> Updated_at: 2026-02-02 13:48:21 UTC  
> Url: https://github.com/boostorg/beast/pull/3075#issuecomment-3831197323  

I believe this is related to the issue briefly described in https://github.com/boostorg/beast/issues/3047.  
  
Asio recommends using `async_immediate` inside composed operations to ensure the correct semantics for immediate completion. For example:  
  
```cpp  
asio::async_immediate(self.get_io_executor(), std::move(self));  
```  
  
Internally, this leads to a call to `dispatch` that looks like:  
  
```cpp  
template <typename CompletionHandler>  
void operator()(CompletionHandler&& handler) const  
{  
  typename associated_immediate_executor<  
    CompletionHandler, executor_type>::type ex =  
      (get_associated_immediate_executor)(handler, ex_);  
  (dispatch)(ex, static_cast<CompletionHandler&&>(handler));  
}  
```  
  
If the handler has an associated executor, `dispatch` wraps it in `asio::detail::work_dispatcher`, which then dispatches the handler on that associated executor.  
  
In this context, the handler is the composed operation object (`self`). When the supplied `completion_token` does not have an associated executor, `self` falls back to using the stream’s executor. As a result, `asio::detail::work_dispatcher` is always involved, and the completion handler is guaranteed to run either on its associated executor or on the stream’s executor.  
  
Our current implementation behaves slightly differently. Instead of passing the composed operation object to `dispatch`, we pass the final handler (`h_`) directly:  
  
```cpp  
auto const ex = this->get_immediate_executor();  
net::dispatch(  
    ex,  
    net::append(std::move(h_), std::forward<Args>(args)...));  
```  
  
Because of this, `dispatch` does not attempt to use `asio::detail::work_dispatcher` with the stream’s executor when the handler lacks an associated executor. Consequently, the earlier guarantee no longer holds:  
  
> and the completion handler is guaranteed to run either on its associated executor or on the stream’s executor.

---
