# #70 - Providing leaf::result with forwarding constructor and emplace method [Closed]

> Username: fedraco-gh  
> Created at: 2024-01-26 14:51:46 UTC  
> Updated at: 2024-01-28 02:40:44 UTC  
> Closed at: 2024-01-28 02:40:44 UTC  
> Url: https://github.com/boostorg/leaf/issues/70  

Would it be possible to expose a `leaf::result` constructor to directly initialize its value from a parameter pack as it is provided by [constructor 9](https://en.cppreference.com/w/cpp/utility/expected/expected) of `std::expected`?  
  
I think it would be useful when dealing with return types that are both expensive to copy and move. For the same reason an `emplace` method might come in handy.

---

## Comment 1

> Username: zajo  
> Created at: 2024-01-26 17:52:17 UTC  
> Url: https://github.com/boostorg/leaf/issues/70#issuecomment-1912454714  

You do this by invoking new_error, which does take a parameter pack:  
  
```  
result<int> f()  
{  
    if( .... )  
        return leaf::new_error(e1, e2, ...);  
}  
```  
  
Note that in LEAF result<> types never hold any error objects, so moving an object of a result<> type is efficient even when e1, e2, ... are extremely expensive to move.

---

## Comment 2

> Username: fedraco-gh  
> Created at: 2024-01-26 18:39:50 UTC  
> Url: https://github.com/boostorg/leaf/issues/70#issuecomment-1912518035  

Thank you for your reply.  
I was referring to the object that is returned if no error arises.  
  
Please refer to this example on Compiler Explorer: https://godbolt.org/z/7nefnxnrv.  
  
With `throwing_foo` there are no copies being made thanks to prvalue materialization rules.  
With `noexcept_foo`, which returns a `leaf::result`, one copy takes place.  
  
If it was possible to initialize leaf::result directly with the T constructor parameters the copy would not happen.
