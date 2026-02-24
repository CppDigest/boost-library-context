# #109 Respect BOOST_NO_FENV_H in execution monitor [Closed]

> Username: rioderelfte  
> Created at: 2017-02-13 13:29:26 UTC  
> Updated at: 2017-03-08 20:28:06 UTC  
> Closed at: 2017-03-08 20:27:39 UTC  
> Url: https://github.com/boostorg/test/pull/109  

`BOOST_NO_FENV_H` is already checked in `boost/test/execution_monitor.hpp` before `<boost/detail/fenv.h>` is included.

---

## Comment 1

> Username: raffienficiaud  
> Created_at: 2017-02-15 21:24:36 UTC  
> Url: https://github.com/boostorg/test/pull/109#issuecomment-280143589  

Please have a look at this ticket: https://svn.boost.org/trac/boost/ticket/11756

---

## Comment 2

> Username: rioderelfte  
> Created_at: 2017-03-08 20:27:39 UTC  
> Url: https://github.com/boostorg/test/pull/109#issuecomment-285158848  

Your proposed changes in the topic/11756-non-standards-fpexception-intel branch work for me. So I am closing this PR. Thanks!

---
