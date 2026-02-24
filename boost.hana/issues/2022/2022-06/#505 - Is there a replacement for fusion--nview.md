# #505 - Is there a replacement for fusion::nview [Closed]

> Username: jarro2783  
> Created at: 2022-06-15 07:43:00 UTC  
> Updated at: 2022-07-08 00:08:52 UTC  
> Closed at: 2022-07-08 00:08:52 UTC  
> Url: https://github.com/boostorg/hana/issues/505  

Is there an equivalent for fusion::nview? I have some code that calls  
```  
fold(nview<T, Indices>(t), ...)  
```  
  
I am trying to improve compile time so I think hana is a good replacement, but I'm not quite sure how to do this.

---

## Comment 1

> Username: tzlaine  
> Created at: 2022-06-15 14:55:00 UTC  
> Url: https://github.com/boostorg/hana/issues/505#issuecomment-1156578964  

This is very easy to do yourself:  
  
```c++  
auto t = /* some hana tuple */;  
hana::fold_left(  
  Indices{}, // should be a sequence of hana integral constants for convenience  
  result_type{},  
  [t](auto result, auto x) {  
    // get the xth element out of t, using the current index x from Indices{}  
    auto t_element = t[x];  
    return /* some function of result and t_element */;  
});  
```

---

## Comment 2

> Username: jarro2783  
> Created at: 2022-06-15 20:52:56 UTC  
> Url: https://github.com/boostorg/hana/issues/505#issuecomment-1156920723  

Ahah, that's what I was looking for, I couldn't quite get my head around it.  
  
Thanks very much.

---

## Comment 3

> Username: jarro2783  
> Created at: 2022-06-15 23:08:55 UTC  
> Url: https://github.com/boostorg/hana/issues/505#issuecomment-1157043050  

Ok next problem is that `t` is a BOOST_FUSION_DEFINE_STRUCT, and I can't access it with `[]`. I'm not sure if I can just convert this to BOOST_HANA_DEFINE_STRUCT and it works, or if I somehow need to fold over the struct.

---

## Comment 4

> Username: tzlaine  
> Created at: 2022-06-16 02:41:10 UTC  
> Url: https://github.com/boostorg/hana/issues/505#issuecomment-1157169217  

I believe it will work if you use the hana version of the adapt macro.  You can also just use the number in each `x` (which is a compile-time constant) above to get the `x`th value out of `t`, even if `t` is a fusion tuple.

---

## Comment 5

> Username: jarro2783  
> Created at: 2022-07-07 23:50:50 UTC  
> Url: https://github.com/boostorg/hana/issues/505#issuecomment-1178379570  

I tried using BOOST_HANA_DEFINE_STRUCT, and that doesn't provide a `[]` operator either. Is there a function that will get the `x`th element out of one of those structs?

---

## Comment 6

> Username: jarro2783  
> Created at: 2022-07-08 00:08:52 UTC  
> Url: https://github.com/boostorg/hana/issues/505#issuecomment-1178390207  

Actually I just managed to get this to work. I used `hana::accessors` to get element x. Thanks for your help.
