# #48 - atomic_shared_ptr and memory_order [Closed]

> Username: Flast  
> Created at: 2018-02-07 05:09:09 UTC  
> Updated at: 2018-03-08 21:37:01 UTC  
> Closed at: 2018-03-08 21:37:01 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/48  

[atomic_shared_ptr](https://github.com/boostorg/smart_ptr/blob/develop/include/boost/smart_ptr/atomic_shared_ptr.hpp) take (unused) memory order via `int` type, however Boost.Atomic's enumeration was changed to scoped enum and inline variable in boostorg/atomic@10c61bb25d4e0fa994bbb684f7158aa7794e92ba (like a upcoming c++ standard).  
  
Some tests show compilation failure caused by above change: scoped enum to `int`  conversion is rejected, [Flast-FreeBSD11-boost-bin-v2-libs-smart_ptr-test-atomic_sp_test-test-clang-linux-4-0~gnu++11-debug](http://www.boost.org/development/tests/develop/developer/output/Flast-FreeBSD11-boost-bin-v2-libs-smart_ptr-test-atomic_sp_test-test-clang-linux-4-0~gnu++11-debug.html).  
  
Replacing `int` with `boost::memory_order` solves the test failure, but using std's value still fail (I don't know who uses std's value, though). IMO, accepting std's value is convenient for user, but not requirement. Any thoughts?

---

## Comment 1

> Username: pdimov  
> Created at: 2018-02-07 13:42:45 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/48#issuecomment-363772186  

I wondered why `sp_atomic_test` didn't fail as well, then noticed that half of the tests are totally missing from http://www.boost.org/development/tests/develop/developer/smart_ptr.html. Hm.

---

## Comment 2

> Username: pdimov  
> Created at: 2018-02-07 13:45:16 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/48#issuecomment-363772972  

http://www.boost.org/development/tests/master/developer/smart_ptr.html still has them. Could this be caused by a Boost.Build change, @swatanabe?

---

## Comment 3

> Username: Flast  
> Created at: 2018-02-07 14:16:01 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/48#issuecomment-363781694  

> http://www.boost.org/development/tests/master/developer/smart_ptr.html still has them. Could this be caused by a Boost.Build change,  
  
The Boost.Atomic change is not yet merged into master branch.

---

## Comment 4

> Username: swatanabe  
> Created at: 2018-02-07 15:37:47 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/48#issuecomment-363807843  

AMDG  
  
On 02/07/2018 06:45 AM, Peter Dimov wrote:  
> http://www.boost.org/development/tests/master/developer/smart_ptr.html still has them. Could this be caused by a Boost.Build change, @swatanabe?  
>   
  
  Which tests are missing?  It looks okay to me now,  
and sp_atomic_test is shown as failing across the  
board in develop.  
  
In Christ,  
Steven Watanabe

---

## Comment 5

> Username: pdimov  
> Created at: 2018-02-07 15:51:26 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/48#issuecomment-363812607  

It's fine now. Must have been a transient upload/download issue. Sorry.

---

## Comment 6

> Username: Lastique  
> Created at: 2018-03-08 21:34:50 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/48#issuecomment-371632109  

> The Boost.Atomic change is not yet merged into master branch.  
  
FYI, it is now. The change is planned to ship in 1.67.

---

## Comment 7

> Username: pdimov  
> Created at: 2018-03-08 21:37:01 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/48#issuecomment-371632722  

https://github.com/boostorg/smart_ptr/commit/4025698fe8583aa085e251e449804aeb4d6e0388
