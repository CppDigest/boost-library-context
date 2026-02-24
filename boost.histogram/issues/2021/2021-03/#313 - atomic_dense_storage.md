# #313 - atomic_dense_storage? [Closed]

> Username: HDembinski  
> Created at: 2021-03-17 15:46:04 UTC  
> Updated at: 2021-03-17 21:00:25 UTC  
> Closed at: 2021-03-17 21:00:25 UTC  
> Url: https://github.com/boostorg/histogram/issues/313  

With https://en.cppreference.com/w/cpp/atomic/atomic/compare_exchange one can atomically modify any object that is trivially copyable, meaning its bits fully represent its state and the states of two objects can be bit-compared. This is true for all the builtin accumulators right now. So we could make one storage that uses this to atomically write to its accumulators. This would make the thread_safe accumulator obsolete and we could suddenly use all accumulators in a thread-safe way.

---

## Comment 1

> Username: HDembinski  
> Created at: 2021-03-17 21:00:25 UTC  
> Url: https://github.com/boostorg/histogram/issues/313#issuecomment-801439523  

Nevermind. It does not work, because we can still only use compare_exchange on arithmetic types.
