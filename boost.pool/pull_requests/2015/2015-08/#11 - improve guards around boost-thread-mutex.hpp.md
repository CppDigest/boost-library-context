# #11 improve guards around boost/thread/mutex.hpp [Merged]

> Username: nschloe  
> Created at: 2015-08-12 17:42:58 UTC  
> Updated at: 2018-03-28 20:23:17 UTC  
> Merged at: 2015-08-12 18:54:57 UTC  
> Closed at: 2015-08-12 18:54:57 UTC  
> Url: https://github.com/boostorg/pool/pull/11  

Fixes bug #7085 (https://svn.boost.org/trac/boost/ticket/7085).

---

## Comment 1

> Username: mclow  
> Created_at: 2015-08-12 18:26:10 UTC  
> Url: https://github.com/boostorg/pool/pull/11#issuecomment-130401602  

So, the goal here is if you define `BOOST_POOL_NO_MT`, then you don't include "mutex.hpp"?

---

## Comment 2

> Username: nschloe  
> Created_at: 2015-08-12 18:31:45 UTC  
> Url: https://github.com/boostorg/pool/pull/11#issuecomment-130404291  

Indeed. The discussion at https://svn.boost.org/trac/boost/ticket/7085 gives more details.

---

## Comment 3

> Username: mclow  
> Created_at: 2015-08-12 18:55:00 UTC  
> Url: https://github.com/boostorg/pool/pull/11#issuecomment-130411173  

Looks good to me. Please watch the test bots and ping me in a few days to merge to master (after the 1.59.0 release is done).

---

## Comment 4

> Username: nschloe  
> Created_at: 2015-08-12 19:13:24 UTC  
> Url: https://github.com/boostorg/pool/pull/11#issuecomment-130415500  

Um, wait. Am I supposed to watch the test bots? Okay. Where can I do that?

---

## Comment 5

> Username: mclow  
> Created_at: 2015-08-12 22:31:11 UTC  
> Url: https://github.com/boostorg/pool/pull/11#issuecomment-130467014  

Here: http://www.boost.org/development/tests/develop/developer/pool.html

---

## Comment 6

> Username: nschloe  
> Created_at: 2015-08-25 12:59:05 UTC  
> Url: https://github.com/boostorg/pool/pull/11#issuecomment-134577566  

> ping me in a few days to merge to master  
  
@mclow Ping!

---

## Comment 7

> Username: nschloe  
> Created_at: 2015-09-03 12:30:04 UTC  
> Url: https://github.com/boostorg/pool/pull/11#issuecomment-137421645  

@mclow Has this been merged to master yet?

---

## Comment 8

> Username: mclow  
> Created_at: 2015-09-03 14:23:52 UTC  
> Url: https://github.com/boostorg/pool/pull/11#issuecomment-137464971  

Just did.

---

## Comment 9

> Username: nschloe  
> Created_at: 2015-09-03 14:25:04 UTC  
> Url: https://github.com/boostorg/pool/pull/11#issuecomment-137465253  

Perfect, thanks!

---
