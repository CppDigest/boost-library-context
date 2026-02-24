# #397 - Documentation of Constant is inaccurate? [Closed]

> Username: DarthRubik  
> Created at: 2018-05-26 21:33:34 UTC  
> Updated at: 2018-05-30 23:09:47 UTC  
> Closed at: 2018-05-30 23:09:47 UTC  
> Url: https://github.com/boostorg/hana/issues/397  

If you look at the [documentation](https://www.boost.org/doc/libs/1_67_0/libs/hana/doc/html/group__group-Constant.html) for the `Constant` concept, it says that the minimal complete definition is the `value` and `to` functions.  But if you look at the code for the `Constant` concept, it only appears to require the `value` function.  
  
So is the documentation wrong?

---

## Comment 1

> Username: ricejasonf  
> Created at: 2018-05-29 22:14:24 UTC  
> Updated at: 2018-05-29 22:22:29 UTC  
> Url: https://github.com/boostorg/hana/issues/397#issuecomment-392964262  

I wouldn't assume that the implementations of the concepts are comprehensive. `Constant` isn't checking for the existence of a `value_type` member either. I dunno if that was omitted deliberately or not.  
  
I'm guessing that `to` is part of the MCD because it can be specialized and must satisfy certain laws,  
but there is a default implementation that uses `value` and `is_convertible`.  
  
Perhaps it should say `value` and (`to` or satisfy `is_convertible` with `value_type`) but it goes on to say that in the discussion of the laws.  
  
Here is the default implementation of `to` for `Constant`:  
```cpp  
    //////////////////////////////////////////////////////////////////////////  
    // Conversion for Constants  
    //////////////////////////////////////////////////////////////////////////  
    template <typename To, typename From>  
    struct to_impl<To, From, when<  
        hana::Constant<From>::value &&  
        is_convertible<typename From::value_type, To>::value  
    >> : embedding<is_embedded<typename From::value_type, To>::value> {  
        template <typename X>  
        static constexpr decltype(auto) apply(X const&)  
        { return hana::to<To>(hana::value<X>()); }  
    };  
```

---

## Comment 2

> Username: ldionne  
> Created at: 2018-05-30 03:06:19 UTC  
> Url: https://github.com/boostorg/hana/issues/397#issuecomment-393015840  

I believe I omitted it deliberately because it can't be checked. The requirement on `to` for a constant with tag `C` holding a value of tag `T` is that for any other `Constant` `i` whose wrapped type is Hana-convertible to `T`, `hana::to<C>(i)` is valid. But it's impossible to check that this is true for _any_ such `i`, as I would have to check them all. So instead I just don't check.  
  
I'm curious to know whether this has bitten you in the wild, or whether you were just curious.

---

## Comment 3

> Username: DarthRubik  
> Created at: 2018-05-30 23:09:44 UTC  
> Url: https://github.com/boostorg/hana/issues/397#issuecomment-393348428  

No....just curious
