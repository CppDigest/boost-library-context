# #226 - infinite waiting [Closed]

> Username: Yanjiachan  
> Created at: 2024-08-01 02:26:00 UTC  
> Updated at: 2026-01-07 09:21:33 UTC  
> Closed at: 2026-01-07 09:21:33 UTC  
> Url: https://github.com/boostorg/interprocess/issues/226  

When using a message_queue, if a process exits or crashes and then restarts, it can cause another process to enter into an infinite wait state.  
waiting at:  
[`p_hdr->m_cond_send.notify_one();`](https://github.com/boostorg/interprocess/blob/develop/include/boost/interprocess/ipc/message_queue.hpp#L1071)

---

## Comment 1

> Username: igaztanaga  
> Created at: 2024-08-05 21:16:23 UTC  
> Url: https://github.com/boostorg/interprocess/issues/226#issuecomment-2269933578  

This is a known issue, if your platform does not support robust synchronization primitives there is no possible correct emulation. You can define BOOST_INTERPROCESS_ENABLE_TIMEOUT_WHEN_LOCKING, so that waits longer than BOOST_INTERPROCESS_TIMEOUT_WHEN_LOCKING_DURATION_MS (10.000 milliseconds by default) will return error.  
  
What platform are you using? Windows?

---

## Comment 2

> Username: Yanjiachan  
> Created at: 2024-08-06 02:24:52 UTC  
> Url: https://github.com/boostorg/interprocess/issues/226#issuecomment-2270246746  

Thanks for your reply, I am using message_queue on linux.  
Is there any relevant documentation about these macro definitions?

---

## Comment 3

> Username: igaztanaga  
> Created at: 2024-08-06 20:47:54 UTC  
> Url: https://github.com/boostorg/interprocess/issues/226#issuecomment-2272123652  

Sadkly, there is no public information that's why I will left this bug open until I document it. In any case, since you are using Linux, starting from Boost 1.78, the implementation should use Robust Mutexes (see https://github.com/boostorg/interprocess/issues/65) , so you should not have these infinite loop problems. Which Boost version are you using?

---

## Comment 4

> Username: stellarpower  
> Created at: 2025-01-20 00:56:59 UTC  
> Url: https://github.com/boostorg/interprocess/issues/226#issuecomment-2601120057  

I am on a recent linux and using Boost 1.85, and I still observe this behaviour. I don't think I've seen the same issues when using named objects in a shared segment - but I'm using the SPSC queue (backed by atomics rather than mutexes) and a more clear-cut producer-consumer pattern  within the region for most communication, so there wouldn't be as many situations requiring locking. One side is creating the object and the other opens it and that's more or less it.  
  
With the `message_queue` however, the sending side is hanging on `p_hdr->m_cond_recv.notify_one()` when the receiving process has not exited cleanly and since been restarted. I'll try setting the timeout preprocessor definition; is there anything else that needs to be done to use these robust mutexes? Or should they be compiled into the implementation by default?

---

## Comment 5

> Username: igaztanaga  
> Created at: 2026-01-07 09:21:33 UTC  
> Url: https://github.com/boostorg/interprocess/issues/226#issuecomment-3717981312  

Note that there is no robust implementation for condition variables, so any interaction after a process crashes with the mutex locked is undefined. AFAIK there is no OS mechanism that can save us.
