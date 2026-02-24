# #111 - `pointer_traits<void*>::template rebind<int>` is not `int*` [Closed]

> Username: cmazakas  
> Created at: 2022-04-26 16:15:25 UTC  
> Updated at: 2022-04-26 18:21:12 UTC  
> Closed at: 2022-04-26 18:21:12 UTC  
> Url: https://github.com/boostorg/core/issues/111  

Relevant Compiler Explorer link: https://godbolt.org/z/xo3xKa34e (credit to Ed Catmur on slack)  
  
Boost version is 1.79  
  
It seems like Core's implementation of `pointer_traits` isn't behaving the same as its STL counterpart and is incorrectly producing a `int**` pointer.

---

## Comment 1

> Username: glenfe  
> Created at: 2022-04-26 16:21:27 UTC  
> Url: https://github.com/boostorg/core/issues/111#issuecomment-1109995746  

Thanks, looks like I introduced this recently. I'll take care of it. The C++11 aliases deserve some tests too.

---

## Comment 2

> Username: glenfe  
> Created at: 2022-04-26 18:21:12 UTC  
> Url: https://github.com/boostorg/core/issues/111#issuecomment-1110112801  

Should be addressed in d74140983da3379d08f0a0bfc3dd36fc6d452100 ( following 1e5c86eb9d639404ba1dadd06d0ddb466e7af4b3 )
