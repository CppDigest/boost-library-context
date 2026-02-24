# #57 Add the coroutine::is_suspended() member function [Open]

> Username: djarek  
> Created at: 2017-10-24 22:04:28 UTC  
> Updated at: 2018-05-16 01:12:45 UTC  
> Url: https://github.com/boostorg/asio/pull/57  

```is_suspended()``` allows checking if the coroutine is in a suspended state. This is especially useful when implementing the ```asio_is_continuation()``` customization hook of composed operations  
based on stackless coroutines.  
@vinniefalco

---

## Comment 1

> Username: vinniefalco  
> Created_at: 2017-10-24 22:27:28 UTC  
> Url: https://github.com/boostorg/asio/pull/57#issuecomment-339154635  

This is a very nice change which will simplify the implementation of composed operations using stackless coroutines!

---

## Review 2 [Commented]

> Username: vinniefalco  
> Created_at: 2018-05-16 01:12:45 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/asio/pull/57#pullrequestreview-120466046  

📁 include/boost/asio/coroutine.hpp

```diff
 259 |+    * reached its terminal state.
 260 |+    */
 261 |+   bool is_suspended() const { return value_ > 0; }
```

> Username: vinniefalco  
> Created_at: 2018-05-16 01:12:45 UTC  
> Url: https://github.com/boostorg/asio/pull/57#discussion_r188479619  

Consider:  
```  
bool is_continuation() const noexcept { return value_ != 0; }  
```


---
