# #16 - Convert invalid characters to U+FFFD REPLACEMENT CHARACTER instead of failing conversion [Closed]

> Username: artyom-beilis  
> Created at: 2017-06-19 12:25:37 UTC  
> Updated at: 2019-12-13 18:26:36 UTC  
> Closed at: 2019-12-13 18:26:36 UTC  
> Url: https://github.com/boostorg/nowide/issues/16  

It is needed for consistent and safer behavior.

---

## Comment 1

> Username: Flamefire  
> Created at: 2019-12-13 18:26:36 UTC  
> Url: https://github.com/boostorg/nowide/issues/16#issuecomment-565553698  

Fixed by https://github.com/boostorg/nowide/commit/11a7d83c1c9ae1bca57ca2be0c5f062946ba517c
