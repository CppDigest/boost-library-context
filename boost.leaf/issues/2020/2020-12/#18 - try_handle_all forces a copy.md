# #18 - try_handle_all forces a copy [Closed]

> Username: mikezackles  
> Created at: 2020-12-09 01:52:10 UTC  
> Updated at: 2020-12-09 03:27:32 UTC  
> Closed at: 2020-12-09 02:33:08 UTC  
> Url: https://github.com/boostorg/leaf/issues/18  

```  
        if( auto r = leaf_detail::try_catch_(  
                ctx,  
                [&]  
                {  
                    return std::forward<TryBlock>(try_block)();  
                },  
                std::forward<H>(h)...) )  
            return r.value();  
```  
  
Shouldn't this be something like `std::move(r).value()`? Otherwise move-only types won't work. For example:  
  
```  
std::unique_ptr<Foo> p = leaf::try_handle_all(  
  [&]() -> leaf::result<std::unique_ptr<Foo>> { return foo(); },  
  [&](leaf::error_info const &) { return nullptr; }  
);  
```

---

## Comment 1

> Username: zajo  
> Created at: 2020-12-09 02:33:08 UTC  
> Url: https://github.com/boostorg/leaf/issues/18#issuecomment-741486507  

Yes, thank you. I have tests for move-only `T` in `result<T>` but somehow missed to test that with `try_handle_all`. This is now fixed on `develop`, and I added the unit tests in https://github.com/boostorg/leaf/blob/develop/test/handle_all_test.cpp#L486-L677.

---

## Comment 2

> Username: mikezackles  
> Created at: 2020-12-09 02:56:27 UTC  
> Url: https://github.com/boostorg/leaf/issues/18#issuecomment-741493219  

:+1: Thanks for being so responsive.

---

## Comment 3

> Username: zajo  
> Created at: 2020-12-09 03:26:55 UTC  
> Updated at: 2020-12-09 03:27:32 UTC  
> Url: https://github.com/boostorg/leaf/issues/18#issuecomment-741502254  

Keep'em coming. :) Any other feedback you have is appreciated as well.
