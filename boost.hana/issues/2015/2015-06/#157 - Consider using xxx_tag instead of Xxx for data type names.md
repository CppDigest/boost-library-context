# #157 - Consider using xxx_tag instead of Xxx for data type names [Closed]

> Username: ldionne  
> Created at: 2015-06-23 19:37:21 UTC  
> Updated at: 2015-09-03 20:48:54 UTC  
> Closed at: 2015-09-03 20:48:54 UTC  
> Url: https://github.com/boostorg/hana/issues/157  

It was suggested a couple of times that we should use e.g. `map_tag` instead of `Map` to denote the tag of Hana maps. One argument is that capitalized names are usually reserved for concepts in C++.

---

## Comment 1

> Username: ldionne  
> Created at: 2015-08-29 20:28:42 UTC  
> Url: https://github.com/boostorg/hana/issues/157#issuecomment-136045281  

Note that renaming `IntegralConstant` to `integral_constant_tag` would allow us to add a concept named `IntegralConstant` representing any `Constant` satisfying `std::is_integral<C::value_type>`. That would open the door for fixing #100 and fixing #132 by providing an automatic model of `Constant` for `IntegralConstant`s.

---

## Comment 2

> Username: ahundt  
> Created at: 2015-08-30 04:11:48 UTC  
> Url: https://github.com/boostorg/hana/issues/157#issuecomment-136083718  

I do like when tags are named `name_tag` and that is consistent with several other boost libraries.

---

## Comment 3

> Username: ldionne  
> Created at: 2015-09-03 20:48:54 UTC  
> Url: https://github.com/boostorg/hana/issues/157#issuecomment-137570194  

Closed by merging #171.
