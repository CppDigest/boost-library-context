# #425 - Link to overloaded function should go to overload landing page [Closed]

> Username: vinniefalco  
> Created at: 2020-09-28 03:23:35 UTC  
> Updated at: 2020-11-10 01:43:52 UTC  
> Closed at: 2020-11-10 01:43:52 UTC  
> Url: https://github.com/boostorg/json/issues/425  

When a javadoc contains a link to an overloaded function, for example `@ref finish` in `stream_parser`, the link should take you to the overload resolution page and not the first overload.

---

## Comment 1

> Username: evanlenz  
> Created at: 2020-10-12 23:55:12 UTC  
> Url: https://github.com/boostorg/json/issues/425#issuecomment-707403538  

The tricky thing about this one is that the Doxygen output links specifically to that definition. Perhaps that's because Doxygen has no concept of an overload list/resolution page to link to? Should this rule be applied universally (except for in the resolution page itself)? Or might there be exceptions?

---

## Comment 2

> Username: vinniefalco  
> Created at: 2020-10-13 01:16:14 UTC  
> Updated at: 2020-10-13 01:16:42 UTC  
> Url: https://github.com/boostorg/json/issues/425#issuecomment-707425650  

I can't think of any exceptions. Doxygen's markdown doesn't let you specify which overload, so the landing page makes the most sense.

---

## Comment 3

> Username: evanlenz  
> Created at: 2020-10-21 04:56:02 UTC  
> Url: https://github.com/boostorg/json/issues/425#issuecomment-713302743  

Should this issue be moved over to the docca project?

---

## Comment 4

> Username: vinniefalco  
> Created at: 2020-10-21 05:04:07 UTC  
> Url: https://github.com/boostorg/json/issues/425#issuecomment-713305195  

Yep, I will do that, and I owe you a reproducible test case

---

## Comment 5

> Username: vinniefalco  
> Created at: 2020-11-10 01:43:52 UTC  
> Url: https://github.com/boostorg/json/issues/425#issuecomment-724395239  

See https://github.com/boostorg/docca/issues/55
