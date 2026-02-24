# #67 Changed to use posix robust mutex [Closed]

> Username: PomLover  
> Created at: 2018-11-09 20:29:58 UTC  
> Updated at: 2021-10-03 19:15:58 UTC  
> Closed at: 2021-08-21 22:31:31 UTC  
> Url: https://github.com/boostorg/interprocess/pull/67  

Changed to use posix robust mutex to allow subsequent mutex lock can grab the lock from previous dead owner to void deadlock. And it also fixed the issue #65.  
  
Hi Reviewers,  
We ran into some issues while we are using boost::interprocess::named_recursive_mutex with posix implementation when the previous owner of the mutex has been dead without releasing the mutex, so all others which are waiting for the same mutex will be blocked forever. And that's why I added this fix in posix mutex implementation to avoid this issue.

---

## Comment 1

> Username: 363568233  
> Created_at: 2018-12-11 05:42:03 UTC  
> Url: https://github.com/boostorg/interprocess/pull/67#issuecomment-446079547  

Which boost version will update it?

---

## Comment 2

> Username: PomLover  
> Created_at: 2018-12-11 17:13:40 UTC  
> Url: https://github.com/boostorg/interprocess/pull/67#issuecomment-446283667  

I don't know, it depends on when the maintainer can merge it.

---

## Comment 3

> Username: 363568233  
> Created_at: 2018-12-17 06:53:12 UTC  
> Url: https://github.com/boostorg/interprocess/pull/67#issuecomment-447740853  

It only changed recursive_mutex.hpp, there is another mutex.hpp need to change？

---

## Comment 4

> Username: 363568233  
> Created_at: 2018-12-17 07:03:58 UTC  
> Url: https://github.com/boostorg/interprocess/pull/67#issuecomment-447742940  

pthread_mutex_consistent(&m_mut);  after it  
Whether it should be use pthread_mutex_unlock(&m_mut) to unlock it?

---

## Comment 5

> Username: PomLover  
> Created_at: 2018-12-17 16:11:07 UTC  
> Url: https://github.com/boostorg/interprocess/pull/67#issuecomment-447900640  

> pthread_mutex_consistent(&m_mut); after it  
> Whether it should be use pthread_mutex_unlock(&m_mut) to unlock it?  
  
The case is going to solve is when the new request to lock the mutex if the previous owner has been dead, the new owner is expecting to get the mutex lock anyway, if unlock is called, the new owner would have to call lock again to grab the lock which is NOT expected behavior.

---

## Comment 6

> Username: PomLover  
> Created_at: 2018-12-17 16:19:34 UTC  
> Url: https://github.com/boostorg/interprocess/pull/67#issuecomment-447903778  

> It only changed recursive_mutex.hpp, there is another mutex.hpp need to change？  
  
Yeah, potentially for semaphore to, but we didn't use them and test them, so I didn't include any of those changes, but only recursive mutex since we are using it and testing it.

---

## Comment 7

> Username: 363568233  
> Created_at: 2018-12-18 01:00:39 UTC  
> Url: https://github.com/boostorg/interprocess/pull/67#issuecomment-448056777  

The way to deal owner dead is different from windows mutex,  windows mutex will release mutex and throw owner_dead_error exception,  Do not posix mutex keep consistent with windows to tell user?

---

## Comment 8

> Username: 363568233  
> Created_at: 2018-12-18 01:02:58 UTC  
> Url: https://github.com/boostorg/interprocess/pull/67#issuecomment-448057228  

I see the boost::interprocess::interprocess_mutex on linux  is using the mutex.cpp default,  the boost::interprocess::interprocess_mutex is used in rbtree_best_fit

---

## Comment 9

> Username: PomLover  
> Created_at: 2018-12-18 15:59:08 UTC  
> Url: https://github.com/boostorg/interprocess/pull/67#issuecomment-448270932  

> The way to deal owner dead is different from windows mutex, windows mutex will release mutex and throw owner_dead_error exception, Do not posix mutex keep consistent with windows to tell user?  
  
Yeah, it could be aligned with windows mutex implementation in boost, but I would prefer to granting mutex to new owner instead of leave it unlocked which behavior is different from Windows API WaitforSingleObject.

---

## Comment 10

> Username: PomLover  
> Created_at: 2018-12-18 16:16:31 UTC  
> Url: https://github.com/boostorg/interprocess/pull/67#issuecomment-448277347  

> I see the boost::interprocess::interprocess_mutex on linux is using the mutex.cpp default, the boost::interprocess::interprocess_mutex is used in rbtree_best_fit  
  
I didn't use rbtree_best_fit, but definitely the mutex.cpp can be changed to use robust mutex too if needed, but I didn't use it, so I would prefer to having someone else who is using and testing it making the changes.

---

## Comment 11

> Username: PomLover  
> Created_at: 2018-12-18 17:13:34 UTC  
> Url: https://github.com/boostorg/interprocess/pull/67#issuecomment-448297492  

Actually I think the new owner release the mutex from previous dead owner is a bad implementation which break some expectations in mutex use cases.

---

## Comment 12

> Username: PomLover  
> Created_at: 2019-11-22 16:07:18 UTC  
> Url: https://github.com/boostorg/interprocess/pull/67#issuecomment-557591970  

Hi Maintainers and Contributors,  
 It's been a while since I submitted this PR, I'm wondering if there are any concerns or suggestions with this PR, and is it possible to merge this in at that some point, could some of major contributors here provide some feedback or suggestions for next step? Thank you!

---

## Comment 13

> Username: msmolensky  
> Created_at: 2021-03-12 17:41:58 UTC  
> Url: https://github.com/boostorg/interprocess/pull/67#issuecomment-797648335  

Recently ran into the same problem. Implemented this solution for both non-recursive and recursive mutexes. I think this PR fixes an important problem and should be accepted

---

## Comment 14

> Username: igaztanaga  
> Created_at: 2021-08-19 22:25:06 UTC  
> Url: https://github.com/boostorg/interprocess/pull/67#issuecomment-902292300  

Hi,  
  
I've found some time review old bugs and patches. I think the proposed patch (and, in general, robust mutex implementation in C++) has some problems.  
  
**Issue 1, Throwing while locking should left the mutex unlocked**: The final implementation (with commit 34fc89a), throws an exception even when the mutex is locked, something that breaks C++ Lock guarantees (std::unique_lock, scoped_lock, etc... asume that an exception when locking guarantees the mutex was no locked).  And nearly every C++ programmer will use a Lock to call the mutex operations.  
  
**Issue 2, Unconditionally marking the mutex as consistent is wrong as only the application can decide on this**: The first implementation (commit aba35f0), marks the mutex as consistent, which is coherent with not throwing an exception, but a mutex should be marked as consistent if the application can guarantee that no data structures have been corrupted while the original owner died (the original owner probably died while modifying shared data protected by the mutex, and that data might be half-modified). According [to the POSIX specification](https://pubs.opengroup.org/onlinepubs/9699919799/functions/pthread_mutex_consistent.html):  
  
"The pthread_mutex_consistent() function is only responsible for notifying the implementation that the state protected by the mutex has been recovered and that normal operations with the mutex can be resumed. **It is the responsibility of the application to recover the state so it can be reused.**"  
  
So it's not possible to take a decision inside "posix_recursive_mutex::lock()" or any other internal function because the application has no chance to inspect the data.  
  
**Issue 3, Impossible to fix Issue 1 and Issue 2 at the same time with POSIX**: And finally, we can't unlock the mutex and thow an exception informing the previous owner died when EOWNERDEAD is returned because unlocking the mutex marks it as unrecoverable forever. So it's not possible to inform the application that the previous owner died without breaking Lock guarantees.  
  
I'm inclined to think that the only sensible implementation when receiving EOWNERDEAD is to mark the mutex as unrecoverable (just unlocking it) and throwing an exception because certainly data might be corrupted. No new callers will be able to lock it again and will receive an exception, but they won't be blocked and can handle the exception and try to recreate the data structure in another shared memory or mapped file.

---

## Comment 15

> Username: PomLover  
> Created_at: 2021-08-23 16:06:12 UTC  
> Url: https://github.com/boostorg/interprocess/pull/67#issuecomment-903910296  

@igaztanaga Thank you very much for your valuable inputs, I think I agree with all your comments above, and I have a few thoughts based on your comments, as you mentioned, application is responsible to decide if the mutex can be set to consistent and reusable, do you think it will be appropriate to provide another interface for application to set mutex to consistent state and continue using it, as I can see there are some needs in this case from different comments above. As in this case, it is going to be harder for application to notify all existing waiters to wake up and start using a new mutex instead, and that could cause a mess in terms of keeping the same order of the waiters after starting using the new mutex, then reusing existing mutex by setting it consistent will be a better fit. As library provider, it will be great to provide more flexibilities for applications to achieve different cases easily. My thought is that in this case, library provider can't assume the applications will not be able to recover from corrupted shared data by using the existing mutex either and mark the mutex as not recoverable. I think I still agree with you that application is responsible to make this decision, but currently there is no interface for application to make this decision either reusing mutex or creating a new one. It seems like creating a new one is the only choice which might not be ideal for some applications and/or even cause other problems.

---

## Comment 16

> Username: igaztanaga  
> Created_at: 2021-08-31 10:28:45 UTC  
> Url: https://github.com/boostorg/interprocess/pull/67#issuecomment-909108691  

I'm not sure which interface would be reasonable. If you directly manage the mutex, then we could have some lock() overload that succeeds but also informs that the owner was dead, this could work for POSIX, I'm not sure about Win32 (Win32 has no notion of "mutex consistency").  
  
The problem is harder to solve when we try to use more advanced clases (such as managed shared memory in Boost.Interprocess), where the mutex is deeply hidden. If locking returns that the owner is dead, user code is many levels away the mutex failure logic, ¿how can the internal logic of Boost.Interprocess call the user code without unlocking the mutex? (because unlocking would permantly declare the mutex as non-recoverable in POSIX). A callback that the user must pass layer by layer until the internal logic of Boost.Interprocess?  
  
The more I think about it, the more I see the issue is really tricky.

---

## Comment 17

> Username: PomLover  
> Created_at: 2021-08-31 20:07:23 UTC  
> Url: https://github.com/boostorg/interprocess/pull/67#issuecomment-909585325  

@igaztanaga Thanks for your new inputs. Here are my additional thoughts. Even though there is no concept of consistency in Win32, but Win32 returns WAIT_ABANDONED indicator for waiter to identify dead owner case, and allow applications to decide what to do either continue using this mutex with the normal behavior or do something else, I think that is what a lot of Windows Applications have been living with. The interface in my mind would be something like "recover", for POSIX, it will need to set mutex consistence, and for WIN32, there is nothing to do in this case, as WIN32 allows to use the same mutex without setting anything as long as the new owner releases it before exiting. I can understand it feels tricky while dealing with different cases in multi-platforms, but that is the benefit of boost library to deal with the hard portion so that applications don't need to worry about the tricky cases in multi-platforms, then a cross-platform applications will behave the same in multi-platforms.

---

## Comment 18

> Username: msmolensky  
> Created_at: 2021-10-03 19:15:57 UTC  
> Url: https://github.com/boostorg/interprocess/pull/67#issuecomment-933009417  

While I agree that in general the library cannot make a decision, in some cases it is possible. In the systems with only one writer, for example, it is safe for the library to declare the mutex is consistent. Can the mutex be policy or configuration based? By default, it can be unrecoverable, but it may be specialized/configured to be consistent.  
  
I still feel that unconditionally preventing an application from accessing the data is too restrictive. While better than deadlocking, it still won't allow the application to function.

---
