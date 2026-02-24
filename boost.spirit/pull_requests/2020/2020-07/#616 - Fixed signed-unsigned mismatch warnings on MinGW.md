# #616 Fixed signed/unsigned mismatch warnings on MinGW. [Closed]

> Username: Lastique  
> Created at: 2020-07-16 12:40:04 UTC  
> Updated at: 2020-07-18 14:17:25 UTC  
> Closed at: 2020-07-18 13:08:26 UTC  
> Url: https://github.com/boostorg/spirit/pull/616  

Use `common_type` trait and explicit casts to avoid comparing `int` and `wchar_t` constant directly, as `wchar_t` may be an unsigned type and cause warnings like this:  
  
```  
./boost/spirit/home/support/char_encoding/standard_wide.hpp: In static member function 'static bool boost::spirit::char_encoding::standard_wide::strict_ischar(int)':  
./boost/spirit/home/support/char_encoding/standard_wide.hpp:86:23: warning: comparison between signed and unsigned integer expressions [-Wsign-compare]  
             return ch >= WCHAR_MIN && ch <= WCHAR_MAX;  
                       ^  
./boost/spirit/home/support/char_encoding/standard_wide.hpp:86:42: warning: comparison between signed and unsigned integer expressions [-Wsign-compare]  
             return ch >= WCHAR_MIN && ch <= WCHAR_MAX;  
                                          ^  
```

---

## Comment 1

> Username: Kojoley  
> Created_at: 2020-07-16 14:54:10 UTC  
> Url: https://github.com/boostorg/spirit/pull/616#issuecomment-659465786  

I remember pointing @djowel about this warning when it was introduced (https://github.com/boostorg/spirit/commit/5a217a5e033d34b016fef41ee95e311bc1a24140#r35987860). It seems that the check is simply pointless and could be removed.  
  
Btw, is not `static_cast<wchar_t>(WCHAR_MIN)` redundant?

---

## Comment 2

> Username: Kojoley  
> Created_at: 2020-07-16 14:56:31 UTC  
> Url: https://github.com/boostorg/spirit/pull/616#issuecomment-659467311  

Ah, the only place where call to it is not pointless, in `toucs4` function.

---

## Comment 3

> Username: Kojoley  
> Created_at: 2020-07-16 15:01:00 UTC  
> Url: https://github.com/boostorg/spirit/pull/616#issuecomment-659470215  

Nevermind, it should not be in `toucs4` in the first place.

---

## Comment 4

> Username: Lastique  
> Created_at: 2020-07-16 15:11:13 UTC  
> Url: https://github.com/boostorg/spirit/pull/616#issuecomment-659476696  

> Btw, is not `static_cast<wchar_t>(WCHAR_MIN)` redundant?  
  
It may or may not be. The point is that the type of `WCHAR_MIN` is not `wchar_t` (in this case it's `unsigned int`), so I explicitly make it `wchar_t` before the possible further conversion. Then, depending on whether `wchar_t` is signed, the conversion to `common_int` will either zero or sign extend. Whereas if you cast to `common_int` directly, the conversion will not take into account `wchar_t` signedness.

---
