# #25 Fix basic_formatting_ostream error on null string [Closed]

> Username: erichkeane  
> Created at: 2016-02-04 23:40:36 UTC  
> Updated at: 2016-02-05 15:43:47 UTC  
> Closed at: 2016-02-04 23:56:55 UTC  
> Url: https://github.com/boostorg/log/pull/25  

See ticket 11956 https://svn.boost.org/trac/boost/ticket/11956  
  
This fix makes the stream work similarly to the std::basic_ostream  
version.  
  
Signed-off-by: Erich Keane erich.keane@intel.com

---

## Comment 1

> Username: Lastique  
> Created_at: 2016-02-04 23:56:55 UTC  
> Url: https://github.com/boostorg/log/pull/25#issuecomment-180109869  

The current code is correct, null C-style strings are invalid input data.

---

## Comment 2

> Username: erichkeane  
> Created_at: 2016-02-05 00:32:00 UTC  
> Url: https://github.com/boostorg/log/pull/25#issuecomment-180123701  

This is inconsistent with std::basic_ostream (which outputs nothing in this case).  Additionally, printf will print "(null)" when doing so.  Why do we want to be inconsistent with basic_ostream?

---

## Comment 3

> Username: Lastique  
> Created_at: 2016-02-05 08:53:00 UTC  
> Url: https://github.com/boostorg/log/pull/25#issuecomment-180256366  

It is consistent with iostreams (see 27.7.3.6.4/3, [ostream.inserters.character]/3) and printf (I don't have C standard at hand, but `printf("%s", (const char*)0);` crashes as expected).

---

## Comment 4

> Username: Lastique  
> Created_at: 2016-02-05 09:05:26 UTC  
> Url: https://github.com/boostorg/log/pull/25#issuecomment-180260110  

BTW, the `printf` part is answered here: http://stackoverflow.com/a/11589479/4636534

---

## Comment 5

> Username: erichkeane  
> Created_at: 2016-02-05 15:43:47 UTC  
> Url: https://github.com/boostorg/log/pull/25#issuecomment-180408762  

Alright, thanks for the consideration.  I appreciate the thorough answer.

---
