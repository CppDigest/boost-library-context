# #312 - Support move-only attributes [Closed]

> Username: andreasbuhr  
> Created at: 2026-02-03 12:51:47 UTC  
> Updated at: 2026-02-14 20:29:27 UTC  
> Closed at: 2026-02-14 20:29:27 UTC  
> Url: https://github.com/boostorg/parser/issues/312  

Boost.Parser avoids copying attributes. So it should be able to handle move-only attribute types.  
  
And this is almost the case, but only almost. A few places in the code prevent it at the moment.

---

## Comment 1

> Username: andreasbuhr  
> Created at: 2026-02-03 12:57:05 UTC  
> Url: https://github.com/boostorg/parser/issues/312#issuecomment-3841157761  

In https://github.com/boostorg/parser/pull/313 I created an example: An expression parser which produces an AST, where the AST nodes are handled through std::unique_ptr. Only a few code places had to be adapted to make it work.

---

## Comment 2

> Username: andreasbuhr  
> Created at: 2026-02-14 20:29:23 UTC  
> Url: https://github.com/boostorg/parser/issues/312#issuecomment-3902469584  

fixed in https://github.com/boostorg/parser/pull/313
