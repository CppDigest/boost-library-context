# #115 - shared_mutex perfomance on linux [Closed]

> Username: kirillv  
> Created at: 2017-02-22 09:01:29 UTC  
> Updated at: 2018-10-09 04:56:02 UTC  
> Closed at: 2018-10-09 04:56:02 UTC  
> Url: https://github.com/boostorg/thread/issues/115  

This question is based on   
https://svn.boost.org/trac/boost/ticket/11798  
On linux mutex is used to guard iternal state of shared_mutex. Windows uses spinlocks instead. It would be great if on linux it would be the same as on windows (spinlocks).  Its not a bug, but an optimisation request. Thanks in advance.

---

## Comment 1

> Username: ned14  
> Created at: 2017-02-22 11:22:23 UTC  
> Url: https://github.com/boostorg/thread/issues/115#issuecomment-281642316  

Mutexes on linux use an internal spin count.

---

## Comment 2

> Username: viboes  
> Created at: 2017-02-22 14:18:50 UTC  
> Url: https://github.com/boostorg/thread/issues/115#issuecomment-281681388  

I recognize that the implementation is not as good as we would like.  
  
Any complete patch improving the performances is welcome. We will need some probes of the improvement.

---

## Comment 3

> Username: viboes  
> Created at: 2018-10-09 04:56:02 UTC  
> Url: https://github.com/boostorg/thread/issues/115#issuecomment-428059491  

Close as there is no PR since more than 6 months
