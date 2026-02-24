# #1717 - ReadHandler does not do what it did in Boost 1.68's Beast [Closed]

> Username: dascandy  
> Created at: 2019-10-04 12:48:11 UTC  
> Updated at: 2024-02-12 15:26:49 UTC  
> Closed at: 2024-02-12 15:26:49 UTC  
> Url: https://github.com/boostorg/beast/issues/1717  

### Version of Beast  
  
from Boost 1.71  
  
### Steps necessary to reproduce the problem  
  
https://www.boost.org/doc/libs/1_71_0/libs/beast/doc/html/beast/ref/boost__beast__http__async_read/overload2.html  
  
Documentation indicates "bytes transferred from stream". This is not the amount of data entered into the buffer and cannot be used as such. This is a change from 1.68 as far as I can tell.  
  
### All relevant compiler information  
  
Checked on clang 9 and gcc 7.4

---

## Comment 1

> Username: vinniefalco  
> Created at: 2019-10-04 12:50:44 UTC  
> Url: https://github.com/boostorg/beast/issues/1717#issuecomment-538383266  

What do you think the read algorithms should return?

---

## Comment 2

> Username: stale[bot]  
> Created at: 2019-11-03 12:58:52 UTC  
> Url: https://github.com/boostorg/beast/issues/1717#issuecomment-549134294  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 3

> Username: dascandy  
> Created at: 2019-11-12 15:34:28 UTC  
> Url: https://github.com/boostorg/beast/issues/1717#issuecomment-552945991  

I expect the argument to my read call to return the number of bytes "read" into my buffer.

---

## Comment 4

> Username: vinniefalco  
> Created at: 2019-11-12 17:42:13 UTC  
> Url: https://github.com/boostorg/beast/issues/1717#issuecomment-553006637  

Which buffer do you mean, the _DynamicBuffer_ parameter? What if there is so much data in this buffer, that the HTTP read operation can complete without reading any additional bytes from the socket? Would you be okay with 0 being returned in this case?

---

## Comment 5

> Username: klemens-morgenstern  
> Created at: 2022-09-24 05:55:56 UTC  
> Url: https://github.com/boostorg/beast/issues/1717#issuecomment-1256874495  

@dascandy We're on 1.80 now, how's this issue looking?
