# #221 - Generator local scope resources are not cleaned up after coroutine object going out of scope [Open]

> Username: SidneyCogdill  
> Created at: 2025-04-03 12:49:34 UTC  
> Updated at: 2025-06-21 09:49:34 UTC  
> Url: https://github.com/boostorg/cobalt/issues/221  

I would have expected the local scope resources within a generator coroutine to be cleaned up at coroutine object destruction, but it didn't.  
  
```cpp  
cobalt::generator<int> gg()  
{  
    boost::scope::defer_guard release = [&] { std::println("exiting gg"); };  
  
    co_yield 10;  
    co_yield 11;  
    co_return 12;  
}  
  
cobalt::task<void> t12()  
{  
    {  
        auto gen = gg();  
        co_await gen;  
    } // <- `gen` out of scope here, `release` should be destructed but it doesn't.  
    std::println("gen is out of scope");  
}  
  
cobalt::main co_main(int, char **)  
{  
    {  
        co_await t12();  
    }  
    std::println("t12 is out of scope");  
    co_return 0;  
}  
  
```  
  
Expected order:  
  
```  
exiting gg  
gen is out of scope  
t12 is out of scope  
```  
  
Actual order:  
  
  
```  
gen is out of scope  
t12 is out of scope  
exiting gg  
```  
  
This is very surprising (considering that `co_await`-ing does have proper cancellation support that cleans up the local scope objects) and it has caused a nasty bug that took me hours to diagnose.

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2025-06-20 14:53:14 UTC  
> Url: https://github.com/boostorg/cobalt/issues/221#issuecomment-2991926591  

The reason for that is that the generator destructor merely cancels the coroutine. This is because it might be doing some async work (e.g. awaiting some op), so I can't guarantee synchronous shutdown.

---

## Comment 2

> Username: SidneyCogdill  
> Created at: 2025-06-20 23:15:14 UTC  
> Url: https://github.com/boostorg/cobalt/issues/221#issuecomment-2993087788  

IMO the coroutine's destruction (`coroutine_handle.destroy()`) should be blocked until all of its children cancellation progresses complete. otherwise this breaks the common sense that reference parameters (in the absence of parallelism) to (parent) local scope variables are guaranteed to outlive until the current function scope exits, which makes the cognitive overhead not that much smaller than just using callbacks.  
  
In [Corral](https://github.com/hudson-trading/corral) which is also strictly single threaded, it's safe to pass local variables as references when calling asynchronous functions (`corral::Task<T>`), without surprising lifetime issues.

---

## Comment 3

> Username: SidneyCogdill  
> Created at: 2025-06-20 23:34:23 UTC  
> Url: https://github.com/boostorg/cobalt/issues/221#issuecomment-2993118380  

PS: what I mean by blocking is not synchronously blocking, but merely asynchronously waiting until all children complete.

---

## Comment 4

> Username: klemens-morgenstern  
> Created at: 2025-06-21 02:46:40 UTC  
> Url: https://github.com/boostorg/cobalt/issues/221#issuecomment-2993270130  

That just doesn't work in C++, I can't have an implicit await somewhere.  
  
I might be able to add a check on the generator so that it gets unwound and not just cancelled if it's suspended as in your case. That's however not a general solution either.

---

## Comment 5

> Username: klemens-morgenstern  
> Created at: 2025-06-21 03:03:39 UTC  
> Url: https://github.com/boostorg/cobalt/issues/221#issuecomment-2993278026  

I just realized I already did that in another way. If you make your generator lazy it'll do what you expect:  
  
  
```cpp  
cobalt::generator<int> gg()  
{  
    boost::scope::defer_guard release = [&] { std::println("exiting gg"); };  
  
    co_yield 10;  
    co_yield 11;  
    co_return 12;  
}  
```

---

## Comment 6

> Username: SidneyCogdill  
> Created at: 2025-06-21 09:49:34 UTC  
> Url: https://github.com/boostorg/cobalt/issues/221#issuecomment-2993492887  

> That just doesn't work in C++, I can't have an implicit await somewhere  
  
you can't `co_await` inside destructor, but you can definitively defer the coroutine promise destruction time by passing it to asynchronous operation callback lambda, which gives you pretty close to async RAII.  
  
i'm not asking you to implement it that way though, since you did mention that lazy generators solve the problem.  
  
i think the doc should add an example on the difference between eager generator vs lazy and warn this (surprising) behavior which may cause lifetime issues (use after free).
