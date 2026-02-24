# #335 - inconsistent ~executor_work_guard() & executor_work_guard::reset() docs [Closed]

> Username: jurko-gospodnetic  
> Created at: 2020-03-30 17:33:08 UTC  
> Updated at: 2020-12-30 01:13:37 UTC  
> Closed at: 2020-12-30 01:13:36 UTC  
> Url: https://github.com/boostorg/asio/issues/335  

`executor_work_guard::~executor_work_guard()` docs state:  
```  
Unless the object has already been reset, or is in a moved-from state, calls on_work_finished() on the stored executor.   
```  
while the `executor_work_guard::reset()` docs do not. On the other hand, both of them have matching behaviour and will trigger executor's `on_work_finished()` if the work is still owned.

---

## Comment 1

> Username: ghost  
> Created at: 2020-12-30 01:13:35 UTC  
> Url: https://github.com/boostorg/asio/issues/335#issuecomment-752293524  

This issue was moved by [chriskohlhoff](https://github.com/chriskohlhoff) to [chriskohlhoff/asio#754](https://github.com/chriskohlhoff/asio/issues/754).
