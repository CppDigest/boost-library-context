# #466 - Tidy up type traits [Closed]

> Username: vinniefalco  
> Created at: 2017-06-11 13:33:45 UTC  
> Updated at: 2018-05-01 18:32:40 UTC  
> Closed at: 2018-05-01 18:32:40 UTC  
> Url: https://github.com/boostorg/beast/issues/466  

Worth 1k words  
![image](https://user-images.githubusercontent.com/1503976/27011403-e9f7b1a2-4e6f-11e7-85e6-e822c52d0e16.png)

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-06-11 15:18:16 UTC  
> Url: https://github.com/boostorg/beast/issues/466#issuecomment-307635305  

See:  
https://github.com/boostorg/core/commit/6153eebc42b07d80afb9d3f90b959361681cfe06

---

## Comment 2

> Username: vinniefalco  
> Created at: 2017-06-11 15:19:54 UTC  
> Url: https://github.com/boostorg/beast/issues/466#issuecomment-307635565  

@glenfe How should this look  
```  
template<class T>  
struct is_sync_read_stream<T, detail::void_t<decltype(  
    std::declval<std::size_t&>() = std::declval<T>().read_some(  
        std::declval<detail::MutableBufferSequence>()),  
    std::declval<std::size_t&>() = std::declval<T>().read_some(  
        std::declval<detail::MutableBufferSequence>(),  
        std::declval<boost::system::error_code&>()),  
            (void)0)>> : std::integral_constant<bool,  
    has_get_io_service<T>::value  
        > {};  
```

---

## Comment 3

> Username: glenfe  
> Created at: 2017-06-11 15:21:59 UTC  
> Url: https://github.com/boostorg/beast/issues/466#issuecomment-307635869  

> See: boostorg/core@6153eeb  
  
Except we're back to the void_t<decltype(expr)> style in Core now. :)

---

## Comment 4

> Username: vinniefalco  
> Created at: 2017-06-11 15:29:01 UTC  
> Updated at: 2017-06-11 15:41:38 UTC  
> Url: https://github.com/boostorg/beast/issues/466#issuecomment-307636742  

What should my example code look like? I see your commit was reverted

---

## Comment 5

> Username: stale[bot]  
> Created at: 2018-04-24 17:52:37 UTC  
> Url: https://github.com/boostorg/beast/issues/466#issuecomment-384022646  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 6

> Username: stale[bot]  
> Created at: 2018-05-01 18:32:31 UTC  
> Url: https://github.com/boostorg/beast/issues/466#issuecomment-385750199  

It looks like this issue has either been abandoned or resolved so I will go ahead and close it. Feel free to re-open this issue if you feel it needs attention, or open new issues as needed. Thanks!
