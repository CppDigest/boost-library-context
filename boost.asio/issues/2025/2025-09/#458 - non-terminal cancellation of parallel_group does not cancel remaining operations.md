# #458 - non-terminal cancellation of parallel_group does not cancel remaining operations [Open]

> Username: pgit  
> Created at: 2025-09-05 07:09:35 UTC  
> Updated at: 2025-09-05 07:40:17 UTC  
> Url: https://github.com/boostorg/asio/issues/458  

**Synopsis:** When signalling non-terminal cancellation to a parallel group, that signal is distributed to all operations correctly. But if only some of the operations react to the requested cancellation type, the remaining ones are not cancelled when the group completes.  
  
**Analysis:** The parallel group handles the cancellation request here:  
  
https://github.com/boostorg/asio/blob/44722ea0246e001e5fbac6a8b3b8ee153882c83e/include/boost/asio/experimental/impl/parallel_group.hpp#L347-L354  
  
Later, when an operation completes as a result of the cancellation signal (e.g., with `errc::operation_canceled`), the following code gets executed:  
  
https://github.com/boostorg/asio/blob/44722ea0246e001e5fbac6a8b3b8ee153882c83e/include/boost/asio/experimental/impl/parallel_group.hpp#L221-L226  
  
However, since `state_->cancellations_requested_` is already incremented by L352, none of the other operations will be cancelled.  
  
**Expected Behavior:** Interaction between cancellation seems tricky. But regardless of how individual operations handle cancellation, once an operation completes and the cancellation condition tells us to cancel, it must do so.  
  
The cancellation type used for that should be *terminal* I suppose, but the cancellation condition callable can also return other types.  
  
Experimentally removing L352 fixed the issue for me, but I don't know if that has other implications.

---

## Comment 1

> Username: pgit  
> Created at: 2025-09-05 07:40:17 UTC  
> Url: https://github.com/boostorg/asio/issues/458#issuecomment-3257386542  

https://godbolt.org/z/Ed9v1rK5K  
  
Maybe this is even only a design issue on how external and internal cancellation of operations in a parallel group should interact.
