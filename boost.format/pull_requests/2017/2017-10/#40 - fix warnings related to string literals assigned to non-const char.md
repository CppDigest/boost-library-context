# #40 fix warnings related to string literals assigned to non-const char * [Merged]

> Username: jeking3  
> Created at: 2017-10-23 17:07:06 UTC  
> Updated at: 2017-10-24 02:58:08 UTC  
> Merged at: 2017-10-24 02:58:05 UTC  
> Closed at: 2017-10-24 02:58:05 UTC  
> Url: https://github.com/boostorg/format/pull/40  

fixes compiler warnings

---

## Comment 1

> Username: mclow  
> Created_at: 2017-10-23 17:35:16 UTC  
> Url: https://github.com/boostorg/format/pull/40#issuecomment-338737273  

What compiler warnings does this fix?

---

## Comment 2

> Username: jeking3  
> Created_at: 2017-10-23 17:38:58 UTC  
> Updated_at: 2017-10-23 17:50:28 UTC  
> Url: https://github.com/boostorg/format/pull/40#issuecomment-338738584  

In something I merged yesterday, this fixes a C++11 warning about deprecated behavior of assigning a literal string to a "char *" as opposed to a "const char *".  Similar to:  
  
https://stackoverflow.com/questions/8356223/assign-a-string-literal-to-a-char

---

## Comment 3

> Username: mclow  
> Created_at: 2017-10-23 18:03:04 UTC  
> Url: https://github.com/boostorg/format/pull/40#issuecomment-338745663  

Make them `constexpr const` ;-)

---

## Comment 4

> Username: jeking3  
> Created_at: 2017-10-23 18:08:43 UTC  
> Updated_at: 2017-10-23 18:10:27 UTC  
> Url: https://github.com/boostorg/format/pull/40#issuecomment-338747303  

Is there a macro for this?  I know of `BOOST_CONSTEXPR_OR_CONST` for C++03 compatibility.  Perhaps `BOOST_CONSTEXPR const` ?  I'd rather not change it right now, as I have a PR queued up behind this for issue #30.

---

## Comment 5

> Username: mclow  
> Created_at: 2017-10-23 18:17:18 UTC  
> Url: https://github.com/boostorg/format/pull/40#issuecomment-338749913  

How about `BOOST_CONSTEXPR const` ?

---
