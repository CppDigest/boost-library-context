# #349 Support: Endian problems [Merged]

> Username: Kojoley  
> Created at: 2018-01-04 19:33:28 UTC  
> Updated at: 2019-03-07 23:14:17 UTC  
> Merged at: 2019-03-07 23:14:13 UTC  
> Closed at: 2019-03-07 23:14:14 UTC  
> Url: https://github.com/boostorg/spirit/pull/349  

- Fixed macro redefinition:  
  - `BOOST_MINIMAL_INTEGER_COVER_OPERATORS`  
  - `BOOST_NO_IO_COVER_OPERATORS`  
  - `BOOST_SPIRIT_ENDIAN_NO_CTORS`  
  - `BOOST_SPIRIT_ENDIAN_DEFAULT_CONSTRUCT`  
- Undef internal macros.  
- Fixed-width integer detection.  
  
Closes #348

---

## Comment 1

> Username: Kojoley  
> Created_at: 2018-01-04 19:37:10 UTC  
> Url: https://github.com/boostorg/spirit/pull/349#issuecomment-355377828  

Also I am not sure why Spirit still does not use Boost.Endian (but I dislike that its io operators cannot be turned off, Spirit one does not have them).

---

## Comment 2

> Username: djowel  
> Created_at: 2018-01-04 23:03:56 UTC  
> Url: https://github.com/boostorg/spirit/pull/349#issuecomment-355424561  

We should probably use Boost.Endian if we can. Does the io operators affect Spirit use?

---

## Comment 3

> Username: Kojoley  
> Created_at: 2018-01-04 23:08:01 UTC  
> Url: https://github.com/boostorg/spirit/pull/349#issuecomment-355425328  

It only includes `iosfwd` so it should not hurt users.

---

## Comment 4

> Username: Kojoley  
> Created_at: 2018-01-07 18:39:35 UTC  
> Url: https://github.com/boostorg/spirit/pull/349#issuecomment-355842751  

Boost.Endian is implemented with bit shifts on a passed type (only recently it started to cast signed to unsigned before shifting :O). Because of that float types are not supported and we cannot simply switch Karma and Qi to it.

---
