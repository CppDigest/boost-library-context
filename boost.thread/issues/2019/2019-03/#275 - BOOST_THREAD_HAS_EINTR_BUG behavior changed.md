# #275 - BOOST_THREAD_HAS_EINTR_BUG behavior changed [Closed]

> Username: austin-beer  
> Created at: 2019-03-27 21:33:10 UTC  
> Updated at: 2019-05-09 19:23:18 UTC  
> Closed at: 2019-05-09 19:23:18 UTC  
> Url: https://github.com/boostorg/thread/issues/275  

In 5b209c2e834a89c93d308aa7ddb1dfd1207d929d, the code that uses `BOOST_THREAD_HAS_EINTR_BUG` was moved from `pthread/mutex.hpp` to `pthread/pthread_mutex_scoped_lock.hpp`. However, `pthread/mutex.hpp` includes `pthread/pthread_mutex_scoped_lock.hpp` **before** it defines `BOOST_THREAD_HAS_EINTR_BUG`. Therefore, the code in `pthread/pthread_mutex_scoped_lock.hpp` no longer sees that `BOOST_THREAD_HAS_EINTR_BUG` has been defined.  
  
Old Behavior (Before 5b209c2e834a89c93d308aa7ddb1dfd1207d929d): The fix for the EINTR bug **is** used unless the user defines `BOOST_THREAD_HAS_NO_EINTR_BUG`.  
  
New Behavior (After 5b209c2e834a89c93d308aa7ddb1dfd1207d929d): The fix for the EINTR bug **is not** used unless the user defines `BOOST_THREAD_HAS_EINTR_BUG`.  
  
Which behavior do we want moving forward? If the old behavior is desired, then we need to move the following three lines from `pthread/mutex.hpp` to `pthread/pthread_mutex_scoped_lock.hpp`:  
```  
#ifndef BOOST_THREAD_HAS_NO_EINTR_BUG  
#define BOOST_THREAD_HAS_EINTR_BUG  
#endif  
```  
  
If the new behavior is desired, then we need to delete the above three lines from `pthread/mutex.hpp` since they no longer do anything and so are confusing.  
  
I think the new behavior is preferred because very few POSIX implementations suffer from the EINTR bug, but there may be other factors that I'm not aware of.

---

## Comment 1

> Username: viboes  
> Created at: 2019-03-28 19:13:09 UTC  
> Url: https://github.com/boostorg/thread/issues/275#issuecomment-477733717  

Ideally this flag should be deduced by a test.  
  
In the absence of an automatic test, I believe we should set it to BOOST_THREAD_HAS_NO_EINTR_BUG or BOOST_THREAD_HAS_EINTR_BUG depending on whether we know it for a specific platform.  
  
In the other cases where we don't know, I believe we should suppose we have the bug until the user states the opposite.

---

## Comment 2

> Username: austin-beer  
> Created at: 2019-03-28 19:49:21 UTC  
> Url: https://github.com/boostorg/thread/issues/275#issuecomment-477745533  

I don't think we can easily determine this via a test.  
  
And I don't think we know which specific platforms have this bug. The original ticket (https://svn.boost.org/trac10/ticket/6200) doesn't say.  
  
So as you said we should assume that we have the bug until the user states the opposite. So we need to move  
```  
#ifndef BOOST_THREAD_HAS_NO_EINTR_BUG  
#define BOOST_THREAD_HAS_EINTR_BUG  
#endif  
```  
from `pthread/mutex.hpp` to either `pthread/pthread_mutex_scoped_lock.hpp` (where it is used) or `detail/config.hpp`.

---

## Comment 3

> Username: austin-beer  
> Created at: 2019-03-28 19:51:31 UTC  
> Url: https://github.com/boostorg/thread/issues/275#issuecomment-477746289  

I can submit a PR if you would like.

---

## Comment 4

> Username: viboes  
> Created at: 2019-03-30 05:34:12 UTC  
> Url: https://github.com/boostorg/thread/issues/275#issuecomment-478209742  

Yes, I said "Ideally".  
  
If  we don't know "which specific platforms have this bug" we are on the "In the other cases where we don't know" part.  
  
And yes, you can sumbit a PR ;-) please.

---

## Comment 5

> Username: austin-beer  
> Created at: 2019-04-01 16:27:02 UTC  
> Url: https://github.com/boostorg/thread/issues/275#issuecomment-478649421  

PR submitted: #276
