# #308 - wait_until fails with "Invalid argument" when called too soon after reboot [Open]

> Username: maflcko  
> Created at: 2020-02-29 20:33:25 UTC  
> Updated at: 2020-04-08 15:42:18 UTC  
> Url: https://github.com/boostorg/thread/issues/308  

`wait_until` accepts a time point that may be in the past. This generally works fine unless the time point is before the machine booted up.  
  
  
The boost implementation will first calculate the difference to the current time: https://github.com/boostorg/thread/blob/boost-1.70.0/include/boost/thread/pthread/condition_variable_fwd.hpp#L270  
  
Right afterward, the difference is added to the internal clock (`detail::internal_chrono_clock::now() + d`). However, the internal clock only starts when the machine was booted up (and not when the unix epoch started). Thus, the resulting timeout that is passed to `do_wait_until` may be negative (in both values, seconds and nanoseconds). Then `pthread_cond_timedwait` throws "Invalid argument".

---

## Comment 1

> Username: maflcko  
> Created at: 2020-03-02 16:01:08 UTC  
> Updated at: 2020-03-02 23:18:35 UTC  
> Url: https://github.com/boostorg/thread/issues/308#issuecomment-593475634  

Boost 1.66 works fine, and Boost 1.67 is the first version that is broken.  
  
#167 landed in 1.67, so it might be related.

---

## Comment 2

> Username: maflcko  
> Created at: 2020-03-02 16:09:22 UTC  
> Url: https://github.com/boostorg/thread/issues/308#issuecomment-593479875  

Specifically this changed line: https://github.com/boostorg/thread/pull/167/files#diff-54d08155c062cef6acafd6452effd61cL344

---

## Comment 3

> Username: maflcko  
> Created at: 2020-03-02 17:46:44 UTC  
> Url: https://github.com/boostorg/thread/issues/308#issuecomment-593527638  

I wrote a test in https://github.com/bitcoin/bitcoin/pull/18232 to illustrate the issue

---

## Comment 4

> Username: fanquake  
> Created at: 2020-03-02 22:30:49 UTC  
> Url: https://github.com/boostorg/thread/issues/308#issuecomment-593657601  

> Boost 1.66 works fine, and Boost 1.76 is the first version that is broken.  
  
Do you mean Boost 1.72.0 here (latest release)? So the broken range is 1.66.0 to 1.72.0?

---

## Comment 5

> Username: maflcko  
> Created at: 2020-03-05 21:35:41 UTC  
> Url: https://github.com/boostorg/thread/issues/308#issuecomment-595459382  

I see two solutions, where at least one is needed to fix the problem:  
  
* Restore the ` if ( d <= CD::zero() ) return cv_status::timeout;`  
* Pass a valid timestruct to `pthread_cond_timedwait` (http://man7.org/linux/man-pages/man3/pthread_cond_timedwait.3p.html says the nsecs value can not be negative: "The abstime argument specified a nanosecond value less than zero or greater than or equal to 1000 million." )

---

## Comment 6

> Username: luke-jr  
> Created at: 2020-03-05 21:41:24 UTC  
> Url: https://github.com/boostorg/thread/issues/308#issuecomment-595461617  

I'm not sure the clock is guaranteed to be positive, so I think the latter fix is probably correct?
