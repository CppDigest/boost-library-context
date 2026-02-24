# #32 - Improve support for cancellation [Closed]

> Username: mzimbres  
> Created at: 2022-10-02 07:55:32 UTC  
> Updated at: 2022-10-23 13:11:12 UTC  
> Closed at: 2022-10-23 13:11:12 UTC  
> Url: https://github.com/boostorg/redis/issues/32  

At the moment the `connection` classes provide support to cancellation with `cancel` member function. Although not common or even advisable, some users may want to cancel operations with timeouts like this  
```cpp  
co_await (conn.async_run(...) || timer.async_wait(...));  
```  
This is working as long as the timeout is not too small e.g. `10ms`. To handle that properly Aedis need to improve cancelation support. Below some parts of a conversation I've had with Richard  
> timer waits and IO reads will be cancellation-aware. But an operation that you have written yourself may not be.  
  
> these operations may want to check the cancellation context hasn't already been cancelled before initiating, and also store a cancellation handler in connected cancellation slots in order to cut short any long-standing operation.  asio coroutines do this pre-initiation check for you https://github.com/boostorg/asio/blob/0af7858e7b5603a2415a30b69e16c7ef1d47a5e9/include/boost/asio/detail/deadline_timer_service.hpp#L251  
  
> also, If you cancel after posting an operation, when the posted code is executed, it may want to check whether the cancellation_state has been cancelled while the posted step was waiting in the executor.  
```cpp  
// some code...  
asio::post(exec, some_next_step(std::move(my_handler));  
// cancellation happens here...  
// some_next_step will not know that the handler was cancelled unless it checks.  
// ...  
// ... some_next_step now executes...  
```  
```cpp  
void some_next_step(auto handler)  
{  
  auto cs = get_canecllation_state(handler);  
  if (cs.cancelled() != asio::cancellation_type::none)  
  {  
    complete(handler, operation_aborted);  
    return;  
  }  
  // ... not cancelled  
}  
```

---

## Comment 1

> Username: mzimbres  
> Created at: 2022-10-02 08:05:40 UTC  
> Url: https://github.com/boostorg/redis/issues/32#issuecomment-1264579979  

Motivation for this issue was #28.

---

## Comment 2

> Username: mzimbres  
> Created at: 2022-10-23 13:11:08 UTC  
> Url: https://github.com/boostorg/redis/issues/32#issuecomment-1288109993  

All forms of cancellation are now supported, for example, implicit with operator || and && or explicit with the cancel member function.
