# #23 Suppress weak vtables warning [Merged]

> Username: Kojoley  
> Created at: 2016-08-31 17:16:27 UTC  
> Updated at: 2016-08-31 19:18:06 UTC  
> Merged at: 2016-08-31 18:56:50 UTC  
> Closed at: 2016-08-31 18:56:50 UTC  
> Url: https://github.com/boostorg/variant/pull/23  



---

## Comment 1

> Username: apolukhin  
> Created_at: 2016-08-31 18:45:09 UTC  
> Updated_at: 2016-08-31 18:45:24 UTC  
> Url: https://github.com/boostorg/variant/pull/23#issuecomment-243861445  

What class produces those warnings?

---

## Comment 2

> Username: Kojoley  
> Created_at: 2016-08-31 18:51:41 UTC  
> Url: https://github.com/boostorg/variant/pull/23#issuecomment-243863397  

```  
In file included from qi/attr.cpp:9:  
In file included from ../../../boost/spirit/include/qi_char.hpp:16:  
In file included from ../../../boost/spirit/home/qi/char.hpp:14:  
In file included from ../../../boost/spirit/home/qi/char/char_parser.hpp:14:  
In file included from ../../../boost/spirit/home/qi/domain.hpp:18:  
In file included from ../../../boost/spirit/home/support/context.hpp:18:  
In file included from ../../../boost/spirit/home/support/nonterminal/expand_arg.hpp:20:  
In file included from ../../../boost/spirit/home/support/string_traits.hpp:16:  
In file included from ../../../boost/spirit/home/support/container.hpp:22:  
In file included from ../../../boost/variant.hpp:22:  
../../../boost/variant/get.hpp:36:50: warning: 'bad_get' has no out-of-line virtual method definitions; its vtable will be emitted in every translation unit [-Wweak-vtables]  
class __attribute__((__visibility__("default"))) bad_get  
                                                 ^  
```

---

## Comment 3

> Username: apolukhin  
> Created_at: 2016-08-31 18:56:58 UTC  
> Url: https://github.com/boostorg/variant/pull/23#issuecomment-243865006  

Thanks for the patch!

---
