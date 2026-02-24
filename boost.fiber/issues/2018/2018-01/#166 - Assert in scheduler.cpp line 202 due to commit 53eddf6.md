# #166 - Assert in scheduler.cpp line 202 due to commit 53eddf6 [Closed]

> Username: savinz  
> Created at: 2018-01-03 14:57:14 UTC  
> Updated at: 2018-01-04 12:16:51 UTC  
> Closed at: 2018-01-04 12:16:51 UTC  
> Url: https://github.com/boostorg/fiber/issues/166  

Thanks for fixing mem leak in timed wait ops!   
I see an assert firing (due to 53eddf651f99263e4effd711feafaa7bb562d392) in scheduler.cpp line 202 ( !ctx->terminated_is_linked() ).  
Looks benign... the mentioned commit changed `algo_->awakened(ctx)` to `schedule(ctx)` in   
sleep2ready_. The asserts in schedule seem to strict with this change.

---

## Comment 1

> Username: olk  
> Created at: 2018-01-03 17:27:07 UTC  
> Url: https://github.com/boostorg/fiber/issues/166#issuecomment-355072085  

Hmm - could you provide code that triggers the problem?

---

## Comment 2

> Username: savinz  
> Created at: 2018-01-04 08:22:22 UTC  
> Url: https://github.com/boostorg/fiber/issues/166#issuecomment-355223861  

Same code as in #162.  
Just realized my error in the above description, scheduler.cpp line 202 is  
`BOOST_ASSERT( ! ctx->wait_is_linked() )` - copy-paste fail, sorry.

---

## Comment 3

> Username: olk  
> Created at: 2018-01-04 09:40:14 UTC  
> Url: https://github.com/boostorg/fiber/issues/166#issuecomment-355238596  

So does this mean you have no error and I can close this issue?

---

## Comment 4

> Username: savinz  
> Created at: 2018-01-04 10:31:33 UTC  
> Url: https://github.com/boostorg/fiber/issues/166#issuecomment-355249213  

There is an issue. The assert in scheduler.cpp line 202 (`BOOST_ASSERT( ! ctx->wait_is_linked() )`)  
is firing after condition_variable wait_until times out. The wait is unlinked after the return from  
`active_ctx->wait_until( timeout_time, lk)` (condition_variable.hpp line 109), but the mentioned  
assert gets checked before that.

---

## Comment 5

> Username: olk  
> Created at: 2018-01-04 11:02:55 UTC  
> Url: https://github.com/boostorg/fiber/issues/166#issuecomment-355255263  

could provide a test app, please?

---

## Comment 6

> Username: savinz  
> Created at: 2018-01-04 11:28:19 UTC  
> Url: https://github.com/boostorg/fiber/issues/166#issuecomment-355259779  

```c++  
#include <boost/fiber/all.hpp>  
#include <chrono>  
  
using namespace std::chrono_literals;  
using namespace boost;  
  
fibers::mutex mtx;  
fibers::condition_variable cv;  
  
void test() {  
    fibers::async([]{  
        std::unique_lock<fibers::mutex> lock(mtx);  
        cv.wait_for(lock, 1s);  
    });  
}  
```  
  
I've build fiber as static library (BOOST_FIBERS_SOURCE, BOOST_FIBERS_STATIC_LINK)  
and link the app with boost 1.65.1 shared libs (debug, valgrind=on, gcc 5.4.1, linux x64).

---

## Comment 7

> Username: olk  
> Created at: 2018-01-04 12:16:51 UTC  
> Url: https://github.com/boostorg/fiber/issues/166#issuecomment-355268253  

ah, sorry - I overread your comment regarding to `BOOST_ASSERT( ! ctx->wait_is_linked() )`, I was still focused on `BOOST_ASSERT( ! ctx->terminated_is_linked() )` ... is fixed now.
