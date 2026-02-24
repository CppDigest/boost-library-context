# #9 - Provide align_up/align_down for arbitrary integers [Closed]

> Username: Lastique  
> Created at: 2019-05-16 00:15:02 UTC  
> Updated at: 2019-05-20 14:54:01 UTC  
> Closed at: 2019-05-20 13:51:12 UTC  
> Url: https://github.com/boostorg/align/issues/9  

It is sometimes useful to align integers other than `size_t` to a certain boundary. Using `size_t` regardless of the user's integer type is not possible if the type is larger than `size_t` (which is difficult to know portably given that `size_t` is platform-dependent).

---

## Comment 1

> Username: glenfe  
> Created at: 2019-05-20 13:51:12 UTC  
> Url: https://github.com/boostorg/align/issues/9#issuecomment-493996132  

Implemented in 8caa2c0af075500a85e49745dafb509ba8c8ddcd

---

## Comment 2

> Username: Lastique  
> Created at: 2019-05-20 14:54:01 UTC  
> Url: https://github.com/boostorg/align/issues/9#issuecomment-494021762  

Thanks!
