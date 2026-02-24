# #5 - Coroutines support [Closed]

> Username: nicber  
> Created at: 2020-02-01 16:31:49 UTC  
> Updated at: 2020-02-02 22:52:19 UTC  
> Closed at: 2020-02-02 22:52:19 UTC  
> Url: https://github.com/boostorg/leaf/issues/5  

Thank you for building this library. I'm using it for embedded development and I'm very happy so far.  
  
One question that has come up is about the support for coroutines. I know that leaf uses `thread_local` storage for its magic. What would happen if a coroutine is suspended and another takes its place, maybe even moving the first coroutine to another thread?

---

## Comment 1

> Username: zajo  
> Created at: 2020-02-01 23:13:48 UTC  
> Url: https://github.com/boostorg/leaf/issues/5#issuecomment-581079453  

The error objects are stored on the stack in a `leaf::context<E...>`. When `activate()` is called, TL pointers of each of the `E...` types are pointed to the objects stored in the `context`. This effectively binds the `context` to the calling thread until `deactivate()` is called. If threads are switched while a `context` object is active, bad things will happen.  
  
That said, the reason why the `context` type is part of the public interface is so that error objects can be transported between threads. See https://zajo.github.io/leaf/#tutorial-async.

---

## Comment 2

> Username: nicber  
> Created at: 2020-02-01 23:34:57 UTC  
> Url: https://github.com/boostorg/leaf/issues/5#issuecomment-581080875  

So, if I understand correctly, everything should work fine as long as no coroutine switches threads.  
  
I imagine that supporting that would be difficult considering the library's design.

---

## Comment 3

> Username: zajo  
> Created at: 2020-02-02 22:52:19 UTC  
> Url: https://github.com/boostorg/leaf/issues/5#issuecomment-581186690  

If the thread doesn't change, obviously there's no problem. If it may, it might be possible to ensure that the relevant `leaf::context` objects are deactivated, but yes, this would work better with threads than with coroutines. Reopen the issue if you have more questions.
