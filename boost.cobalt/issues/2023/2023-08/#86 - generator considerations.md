# #86 - generator considerations [Closed]

> Username: klemens-morgenstern  
> Created at: 2023-08-21 06:58:58 UTC  
> Updated at: 2023-08-31 08:01:23 UTC  
> Closed at: 2023-08-31 08:01:22 UTC  
> Url: https://github.com/boostorg/cobalt/issues/86  

Apparently the eager generator is confusing so let's sort this out. I think pro-active is bull, an eager coroutine should always be pro-active (which is eager).  
  
1. eager:  
  
```cpp  
generator<T> foo(U thingy)  
{  
    auto res= co_await do_something_with_thingy(thingy);  
    thingy = co_yield res;  
    // use the thingy to do the next thing.  
    res = co_await do_something_with_thingy(thingy);  
    co_return res;  
}  
```  
  
Transaction: U0 gets passed in as arg, yield U0, gets T1 in return, U1 -> T2.  
  
Thus for the caller: co_await passes in T1, gets U0.   
  
```cpp  
auto g = gen(u0);  
t(u0) = co_await g(u1);  
t(u1) = co_await g(u2);  
t(u2) = co_await g(u3);  
```  
  
Lazy would be:  
  
```cpp  
auto g = gen();  
t(u0) = co_await g(u0);  
t(u1) = co_await g(u1);  
t(u2) = co_await g(u2);  
```  
  
Which means we need to get u0 into the coro somehow, without `co_yield`.  
  
```cpp  
generator<T> foo()  
{  
    U thingy = co_await this_coro::initial;  
    auto res= co_await do_something_with_thingy(thingy);  
    thingy = co_yield res;  
    // use the thingy to do the next thing.  
    res = co_await do_something_with_thingy(thingy);  
    co_return res;  
}  
```  
  
Example uses:  
  
Eager could be used for a price ticker to get data quickly (`co_await g_price()`) -> fast & cached  
  
Lazy could be used for DBs with internal managed (`co_await g_sql("select * from tb")`).

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2023-08-21 13:14:03 UTC  
> Url: https://github.com/boostorg/cobalt/issues/86#issuecomment-1686307950  

python's generators are lazy AND you need to send `None` as first value if you want to push. I think the run-time design is correct. `.lazy() const` should be a member function on the `generator` handle.

---

## Comment 2

> Username: klemens-morgenstern  
> Created at: 2023-08-21 13:29:10 UTC  
> Url: https://github.com/boostorg/cobalt/issues/86#issuecomment-1686333403  

The lazy attribute would be known when the generator is constructed, because the init is the first thing the generator would need to do.   
  
On another note: python allows `athrow(ex)` to throw an exception into the generator. Weird, but sorta makes sense. Possible syntax:  
  
```cpp  
co_await g(std::rethrow_exception, ex_ptr);  
```

---

## Comment 3

> Username: klemens-morgenstern  
> Created at: 2023-08-31 08:01:22 UTC  
> Url: https://github.com/boostorg/cobalt/issues/86#issuecomment-1700553371  

Closing. Throwing exceptions into the generator can be added if ever requests. Not really necessary for C++ as much as in python, bc we do a proper unwind.
