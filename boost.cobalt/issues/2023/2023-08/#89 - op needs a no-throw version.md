# #89 - op needs a no-throw version [Closed]

> Username: klemens-morgenstern  
> Created at: 2023-08-22 05:45:37 UTC  
> Updated at: 2023-09-04 14:26:09 UTC  
> Closed at: 2023-09-04 14:26:09 UTC  
> Url: https://github.com/boostorg/cobalt/issues/89  

as_tuple(use_op) works, but a custom op will still throw.   
  
```cpp  
  
co_await my_custom_op(std::nothrow)   
  
co_await @my_custom_op();  
  
```  
  
@ could be any prefix operator, there is however not an intuitive one. `!` seems like it'd do the opposite.

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2023-08-31 08:08:48 UTC  
> Url: https://github.com/boostorg/cobalt/issues/89#issuecomment-1700563707  

I think `.as_tuple` and `.as_result` are good options, that would also work well on channels & other   
awaitables.

---

## Comment 2

> Username: klemens-morgenstern  
> Created at: 2023-09-01 17:24:12 UTC  
> Url: https://github.com/boostorg/cobalt/issues/89#issuecomment-1703090992  

Free function also works, if we overload await_resume().  
  
  
```cpp  
T await_resume(std::nothrow_t);  
```  
  
and then  
  
```cpp  
co_await as_result(my_custom_op);  
````  
  
This would make it more consistent especially for third party awaitables. Similarly, it is similar to as_tuple in asio.
