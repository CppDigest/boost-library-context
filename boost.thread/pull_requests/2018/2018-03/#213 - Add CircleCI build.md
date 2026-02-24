# #213 Add CircleCI build [Merged]

> Username: thughes  
> Created at: 2018-03-05 21:23:12 UTC  
> Updated at: 2018-04-11 21:24:25 UTC  
> Merged at: 2018-03-07 18:53:35 UTC  
> Closed at: 2018-03-07 18:53:36 UTC  
> Url: https://github.com/boostorg/thread/pull/213  

As discussed in #212.  
  
Some notes:  
* Open source projects get 4 Linux containers by default (4 parallel builds)  
* Open source projects can request macOS executors. I sent in a request and it looks like they gave me a single one; you may be able to request more if necessary.  
  *  Someone in the `boostorg` group will need to enable the build on CircleCI for it to start building against the `boostorg/thread` repo: https://circleci.com/gh/boostorg/thread  
  
Still seeing timing issues with some of the lock measurement timing, especially on macOS VMs. I think something like this would fix it if it's measuring the same thing you were originally intending to measure (time to acquire the lock): https://github.com/thughes/thread/commit/d9536b82e0328cf1ce09b68a86d8404ea4c6c5d5. The issue that I've seen is that the `boost::this_thread::sleep_for` can oversleep (as allowed) on the VMs, so just subtracting the requested sleep time of 250ms in the calculation will not give an accurate result.

---

## Comment 1

> Username: viboes  
> Created_at: 2018-03-06 17:25:56 UTC  
> Url: https://github.com/boostorg/thread/pull/213#issuecomment-370860232  

You are right the 250ms in this case wrong as the thread is sleeping for 250ms, so at least we should add 50ms.  
  
However your changes will measure the time for the `unlock()`+`join()` not the `try_to_lock`.

---

## Comment 2

> Username: thughes  
> Created_at: 2018-03-06 17:35:42 UTC  
> Url: https://github.com/boostorg/thread/pull/213#issuecomment-370863423  

Hmm, I don't think so. I'm just doing the calculation in the main thread after the join to avoid concurrent access to `t1`.  
  
`t0` is taken just before the main thread calls `unlock` and `t1` is taken in the second thread immediately after it acquires the lock; it's not waiting for the `join`.

---

## Comment 3

> Username: viboes  
> Created_at: 2018-03-06 19:32:12 UTC  
> Url: https://github.com/boostorg/thread/pull/213#issuecomment-370899943  

The test measured the time take by a successful call to  
  
```  
boost::unique_lock<boost::mutex> lk(m, boost::try_to_lock)  
```  
  
Your measure the time for  
  
```  
   m.unlock();  
   t.join();  
```

---

## Comment 4

> Username: thughes  
> Created_at: 2018-03-06 19:48:56 UTC  
> Url: https://github.com/boostorg/thread/pull/213#issuecomment-370905318  

Yes, technically it includes the `m.unlock` as well as the time for the context switch to the other thread triggered by `join`.  
  
> You are right the 250ms in this case wrong as the thread is sleeping for 250ms, so at least we should add 50ms.  
  
Where does the 50 ms come from?

---

## Comment 5

> Username: viboes  
> Created_at: 2018-03-06 20:16:15 UTC  
> Url: https://github.com/boostorg/thread/pull/213#issuecomment-370913431  

I use to give 50ms to the cost of an operation. It is often too much, but this is what I've taken as delta.

---

## Comment 6

> Username: thughes  
> Created_at: 2018-03-06 21:27:47 UTC  
> Url: https://github.com/boostorg/thread/pull/213#issuecomment-370934453  

How about trying to measure the sleep time and subtract that?  
```  
time_point t3 = Clock::now();  
boost::this_thread::sleep_for(ms(250));  
time_point t4 = Clock::now();  
```  
It also adds overhead, but it seems like it could be more precise than than a constant 50 ms.

---

## Comment 7

> Username: viboes  
> Created_at: 2018-03-07 06:57:36 UTC  
> Updated_at: 2018-03-07 06:59:16 UTC  
> Url: https://github.com/boostorg/thread/pull/213#issuecomment-371042589  

Yes, `t4-t3`could replace the 250ms. We will need then to check for `t4-t3+50ms`.  
This will measure the time for the `unlock()` plus `boost::try_to_lock`

---

## Comment 8

> Username: thughes  
> Created_at: 2018-03-07 17:26:06 UTC  
> Url: https://github.com/boostorg/thread/pull/213#issuecomment-371215567  

Could we could move `t4` after the `m.unlock()` in the main thread to try to account for it directly instead of using 50ms?  
  
I'm just trying to avoid having semi-arbitrary constants that vary by both OS and processor in there combined with making the test case rely on another semi-arbitrary threshold (`max_diff`) to determine success or failure; it seems destined to have problems (or just have so much additional arbitrary time buffer as to not be useful in actually testing anything).

---

## Comment 9

> Username: viboes  
> Created_at: 2018-03-07 18:34:13 UTC  
> Url: https://github.com/boostorg/thread/pull/213#issuecomment-371237967  

I understand your position.   
  
The 50ms is something like a QoI. If an operation takes more than that, there is surely a problem.  
On some platforms (e.g. VM) I believe those tests should be warnings.  
  
I don't think moving the measure will change anything. The operation you want to measure should take some time. This is what these tests are measuring. How long do you think is acceptable for a call to a `try_lock` once the mutex is unlock? It is clear that this depends on how busy the machine is.  
  
A PR that allows to configure at compile time whether these kind of timing checks are errors or warnings would be a good idea.   
  
An alternative idea is to configure at compile time the time acceptable for an operation.  
  
With these PR, we could be able to change the delta acceptable or move those checks to warnings.     
  
Would you be interested in such a PR?

---

## Comment 10

> Username: viboes  
> Created_at: 2018-03-07 18:35:49 UTC  
> Url: https://github.com/boostorg/thread/pull/213#issuecomment-371238478  

> Someone in the boostorg group will need to enable the build on CircleCI for it to start building against the boostorg/thread repo: https://circleci.com/gh/boostorg/thread  
  
It seems that we have already do that, as the Boost release are built already on CircleCI.  
What I could do?

---

## Comment 11

> Username: viboes  
> Created_at: 2018-03-07 18:43:52 UTC  
> Url: https://github.com/boostorg/thread/pull/213#issuecomment-371241104  

Boost.Geometry is already using CircleCI.  
https://circleci.com/gh/boostorg/geometry/tree/master  
  
What should I do from my side?

---

## Comment 12

> Username: viboes  
> Created_at: 2018-03-07 18:51:35 UTC  
> Url: https://github.com/boostorg/thread/pull/213#issuecomment-371243652  

CircleCi doc says to create a .circleci/config.yml file, not a circle.yml file

---

## Comment 13

> Username: viboes  
> Created_at: 2018-03-07 18:53:27 UTC  
> Url: https://github.com/boostorg/thread/pull/213#issuecomment-371244267  

I'll merge it to see if I reaches to setup the account.

---

## Comment 14

> Username: thughes  
> Created_at: 2018-03-07 18:53:43 UTC  
> Url: https://github.com/boostorg/thread/pull/213#issuecomment-371244357  

"boostorg/geometry" is a separate project from the perspective of CircleCI (each repo in the "boostorg" is a separate project), so it will have to specifically be enabled on this page with someone who is part of the "boostorg" team/org: https://circleci.com/gh/boostorg/thread.  
  
> CircleCi doc says to create a .circleci/config.yml file, not a circle.yml file  
Correct, though `circle.yml` is also allowed as you can see in my repo:  https://circleci.com/gh/thughes/thread/tree/feature%2Fadd_circleci

---

## Comment 15

> Username: viboes  
> Created_at: 2018-03-07 18:58:49 UTC  
> Url: https://github.com/boostorg/thread/pull/213#issuecomment-371246047  

Ok. I'll try it.  
  
https://circleci.com/gh/boostorg/workflows/thread

---

## Comment 16

> Username: viboes  
> Created_at: 2018-03-07 22:34:30 UTC  
> Url: https://github.com/boostorg/thread/pull/213#issuecomment-371309693  

It works. The duration is very good.  
  
Now it is time to fix the timing issues.  
  
Thanks a lot,  
Vicente

---

## Comment 17

> Username: viboes  
> Created_at: 2018-03-09 20:33:06 UTC  
> Url: https://github.com/boostorg/thread/pull/213#issuecomment-371937441  

Do you have a PR where you apply only once this change?  
I will be interested in.  
I'll be out for 1 week.

---

## Comment 18

> Username: viboes  
> Created_at: 2018-03-09 20:34:32 UTC  
> Url: https://github.com/boostorg/thread/pull/213#issuecomment-371937789  

BTW, my changes are not enough. I have yet some issues on some tests that I'll look at later on.  
I've been forced to re-run 1 job to get a green CI :(  
  
https://circleci.com/workflow-run/ab368099-02d9-4030-a82c-6cc0e3988204

---

## Comment 19

> Username: thughes  
> Created_at: 2018-03-20 04:52:38 UTC  
> Url: https://github.com/boostorg/thread/pull/213#issuecomment-374475658  

Here's an example of the change to subtract the measured sleep time (instead of exactly 250ms) in one of the tests: https://github.com/thughes/thread/commit/8cac50b6b7891b82b95d6efeb2a62a618debf616

---

## Comment 20

> Username: thughes  
> Created_at: 2018-04-10 16:09:24 UTC  
> Url: https://github.com/boostorg/thread/pull/213#issuecomment-380157851  

@viboes ping ^

---

## Comment 21

> Username: viboes  
> Created_at: 2018-04-11 14:46:26 UTC  
> Url: https://github.com/boostorg/thread/pull/213#issuecomment-380479042  

I see what you are doing, but I want to measure the time of the tested operation, not the whole test.

---

## Comment 22

> Username: thughes  
> Created_at: 2018-04-11 16:29:34 UTC  
> Url: https://github.com/boostorg/thread/pull/213#issuecomment-380514565  

You could move [`t4`](https://github.com/thughes/thread/commit/8cac50b6b7891b82b95d6efeb2a62a618debf616#diff-e6b82d4298c93d5694b6a6aceb1343ddR101) to after the `m.unlock` to get closer.  
  
I guess the problem is that since on CircleCI the sleep time is highly variable (50 - 100 ms over requested time) by always subtracting exactly 250 ms, the calculation is even more wrong since that extra sleep time swamps the actual locking time.

---

## Comment 23

> Username: thughes  
> Created_at: 2018-04-11 16:38:22 UTC  
> Url: https://github.com/boostorg/thread/pull/213#issuecomment-380517371  

Actually, I'm a bit confused as to how the two calculations are actually different. In the case where `boost::this_thread::sleep_for(ms(250));` actually sleeps for exactly 250 ms, the two calculations are exactly the same: `t1 - t0 - 250`. There is some overhead from calling `Clock::now()` twice; perhaps that is non-negligible?  
  
Note that I only moved the location of [`t1`](https://github.com/thughes/thread/commit/8cac50b6b7891b82b95d6efeb2a62a618debf616#diff-e6b82d4298c93d5694b6a6aceb1343ddR64) because in the original code is actually measuring the time for lock + unlock since it measures when the `unique_lock` is no longer in scope. For the purposes of accurately accounting for thread sleep time, you could ignore this part of the change.

---

## Comment 24

> Username: viboes  
> Created_at: 2018-04-11 21:24:25 UTC  
> Url: https://github.com/boostorg/thread/pull/213#issuecomment-380600387  

Oh, I mixed where t1,t0 are stored and where they are used.  
  
Your algo is good.   
  
Please, could you provide a PR?

---
