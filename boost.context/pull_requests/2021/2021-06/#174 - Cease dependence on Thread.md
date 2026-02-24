# #174 Cease dependence on Thread [Merged]

> Username: Kojoley  
> Created at: 2021-06-12 15:21:49 UTC  
> Updated at: 2021-07-15 13:39:39 UTC  
> Merged at: 2021-07-15 04:43:20 UTC  
> Closed at: 2021-07-15 04:43:20 UTC  
> Url: https://github.com/boostorg/context/pull/174  

On C++11 static local variables are initialized in thread-safe manner, but even on C++03 it should not be a problem because in our case variables are of trivial types, which means double initialization is not an issue, and they are initialized with the same value in every thread.

---

## Comment 1

> Username: pdimov  
> Created_at: 2021-06-12 19:09:56 UTC  
> Url: https://github.com/boostorg/context/pull/174#issuecomment-860096345  

Looks good. If accepted, be sure to delete the dependency from `CMakeLists.txt` as well: https://github.com/boostorg/context/blob/131ec9db2416a6840b56d62e1e39262274ea31b5/CMakeLists.txt#L177-L178

---

## Comment 2

> Username: olk  
> Created_at: 2021-07-13 18:38:42 UTC  
> Updated_at: 2021-07-13 18:39:40 UTC  
> Url: https://github.com/boostorg/context/pull/174#issuecomment-879313222  

But because of the dependency of boost.asio to boost.coroutine boost.context must be compile able with C++03 compilers too!  
So  static local variables are not initialized in a thread safe manner and `call_once()` is still required.

---

## Comment 3

> Username: Kojoley  
> Created_at: 2021-07-13 20:02:34 UTC  
> Url: https://github.com/boostorg/context/pull/174#issuecomment-879363809  

> But because of the dependency of boost.asio to boost.coroutine boost.context must be compile able with C++03 compilers too!  
> So static local variables are not initialized in a thread safe manner and `call_once()` is still required.  
  
Did you read the PR description? I'll quote:  
  
> but even on C++03 it should not be a problem because in our case variables are of trivial types, which means double initialization is not an issue, and they are initialized with the same value in every thread.

---

## Comment 4

> Username: olk  
> Created_at: 2021-07-15 04:43:04 UTC  
> Url: https://github.com/boostorg/context/pull/174#issuecomment-880386549  

OK, ty

---

## Comment 5

> Username: olk  
> Created_at: 2021-07-15 04:43:24 UTC  
> Url: https://github.com/boostorg/context/pull/174#issuecomment-880386630  

ty

---

## Comment 6

> Username: Kojoley  
> Created_at: 2021-07-15 13:39:39 UTC  
> Url: https://github.com/boostorg/context/pull/174#issuecomment-880703054  

np, I have almost identical PR against Boost.Coroutine library https://github.com/boostorg/coroutine/pull/58, I would appreciate a merge there too.

---
