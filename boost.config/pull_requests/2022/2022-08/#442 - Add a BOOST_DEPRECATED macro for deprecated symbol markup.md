# #442 Add a BOOST_DEPRECATED macro for deprecated symbol markup [Merged]

> Username: Lastique  
> Created at: 2022-08-14 13:15:44 UTC  
> Updated at: 2022-10-05 12:37:36 UTC  
> Merged at: 2022-10-05 12:26:35 UTC  
> Closed at: 2022-10-05 12:26:35 UTC  
> Url: https://github.com/boostorg/config/pull/442  

`BOOST_DEPRECATED` can be used to mark functions, types and objects as deprecated, with a message with a recommendation of replacement. Using such marked symbols in code will generate compiler warnings, with the  
specified message, if possible.  
  
The warnings can be suppressed if `BOOST_ALLOW_DEPRECATED_SYMBOLS` is defined. Additionally, added support `BOOST_ALLOW_DEPRECATED` macro that not only allows for deprecated symbols but also deprecated headers (i.e. defining  
`BOOST_ALLOW_DEPRECATED` is equivalent to defining both `BOOST_ALLOW_DEPRECATED_SYMBOLS` and `BOOST_ALLOW_DEPRECATED_HEADERS`).  
  
Closes https://github.com/boostorg/config/issues/280.

---

## Review 1 [Approved]

> Username: Flamefire  
> Created_at: 2022-09-12 18:42:17 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/config/pull/442#pullrequestreview-1104653587  

Yes please, this is long overdue!  
  
Looks good to me.

---

## Comment 2

> Username: Lastique  
> Created_at: 2022-10-04 23:00:50 UTC  
> Url: https://github.com/boostorg/config/pull/442#issuecomment-1267696165  

Gentle ping @jzmaddock.  
  
Also CC @pdimov as this touches `BOOST_HEADER_DEPRECATED`.

---

## Comment 3

> Username: pdimov  
> Created_at: 2022-10-04 23:03:57 UTC  
> Url: https://github.com/boostorg/config/pull/442#issuecomment-1267698241  

I'm OK with that addition to `BOOST_HEADER_DEPRECATED`.

---

## Comment 4

> Username: jzmaddock  
> Created_at: 2022-10-05 11:39:38 UTC  
> Url: https://github.com/boostorg/config/pull/442#issuecomment-1268320557  

Sorry I missed this, looks good to me too.  
  
One quick question: should we append to each message instructions on how to suppress the warning, or do we leave this up to individual uses?  Seems like it might be something that is useful in every use?

---

## Comment 5

> Username: Lastique  
> Created_at: 2022-10-05 11:48:46 UTC  
> Updated_at: 2022-10-05 11:49:21 UTC  
> Url: https://github.com/boostorg/config/pull/442#issuecomment-1268330407  

I would prefer to leave the message up to the users of the macro. There may be use cases where the mechanism of disabling the warnings is different from `BOOST_ALLOW_DEPRECATED(_SYMBOLS)`. For example, if the library has its own local config macro for disabling warnings only from that particular library.  
  
Besides, modifying the message implies a specific format or wording of the message specified by user (i.e. does it start with a capital? does it end with a dot so that the instructions start with a new sentence? does it end with a space? etc.) I don't want to make such assumptions.

---

## Comment 6

> Username: jzmaddock  
> Created_at: 2022-10-05 12:26:30 UTC  
> Url: https://github.com/boostorg/config/pull/442#issuecomment-1268368719  

OK, lets go with this.

---
