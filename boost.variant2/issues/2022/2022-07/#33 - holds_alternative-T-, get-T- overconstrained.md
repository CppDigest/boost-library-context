# #33 - holds_alternative<T>, get<T> overconstrained [Closed]

> Username: pdimov  
> Created at: 2022-07-31 15:47:36 UTC  
> Updated at: 2026-01-22 00:42:25 UTC  
> Closed at: 2026-01-22 00:42:25 UTC  
> Url: https://github.com/boostorg/variant2/issues/33  

There's no good reason for these to not compile when T occurs more than once in the list of alternatives, as it's possible to make them work as expected. In the unlikely case the static_assert is desired, the user can easily write it himself.

---

## Comment 1

> Username: pdimov  
> Created at: 2022-10-05 17:10:19 UTC  
> Url: https://github.com/boostorg/variant2/issues/33#issuecomment-1268705073  

The counterargument here is that in cases like `variant<time_t, ptrdiff_t>`, sometimes these can end up being the same type after porting the program to a different environment, and `get<time_t>` will silently succeed when code has (tried to) put a `ptrdiff_t` into the variant. At present, `get<time_t>` would fail to compile after the port, which would catch the problem.

---

## Comment 2

> Username: pdimov  
> Created at: 2026-01-14 15:01:18 UTC  
> Url: https://github.com/boostorg/variant2/issues/33#issuecomment-3749975864  

Motivating example:  
```  
template<typename... Ts>  
auto when_any(task<Ts>... tasks)  
    -> task<variant<Ts...>>;  
```  
In the homogeneous case, where all `tasks...` return the same type `T`, it would be very convenient if `get<T>` worked on the return value (it's guaranteed to succeed.)  
  
Without this, the homogeneous case needs to be special cased as  
```  
template<class T>  
auto when_any(task<T>... tasks)  
    -> task<pair<size_t, T>>;  
```

---

## Comment 3

> Username: pdimov  
> Created at: 2026-01-14 18:08:09 UTC  
> Url: https://github.com/boostorg/variant2/issues/33#issuecomment-3750928778  

https://godbolt.org/z/53hPG85rP

---

## Comment 4

> Username: pdimov  
> Created at: 2026-01-22 00:42:25 UTC  
> Url: https://github.com/boostorg/variant2/issues/33#issuecomment-3781889152  

Implemented on develop.
