# #348 - Tolerable delay in interruptible_wait on Win32 is too long [Open]

> Username: BrukerJWD  
> Created at: 2021-07-08 09:36:07 UTC  
> Updated at: 2021-08-27 17:11:10 UTC  
> Url: https://github.com/boostorg/thread/issues/348  

- Boost 1.74  
- MSVC 2019  
- Windows 10  
  
We have a thread that shall perform small actions in various time intervals. We implemented it with a `condition_variable::wait_for(interval)`:  
  
    bool terminating = false;  
    void workerMain() {  
        while (true) {  
            interval = calculate_time_until_next_action();  
            if (condition_variable.wait_for(lock, interval, [&]() { return terminating; })) {  
                break;  
            }  
            do_some_action();  
        }  
    }  
  
We noticed rather big delays when doing this with longer times, like several minutes. A wait for 20mins always takes 21mins. This is my reproducer:  
  
    boost::mutex m;  
    boost::condition_variable cv;  
    boost::unique_lock lock { m };  
    const boost::chrono::minutes duration { 20 };  
    const auto start = std::chrono::steady_clock::now();  
    cv.wait_for(lock, duration);  
    const auto stop  = std::chrono::steady_clock::now();  
    std::cout << "took " << std::chrono::duration_cast<std::chrono::duration<double>>(stop - start).count() << "s";  
  
The output clearly shows our issue: "took 1260.32s".  
  
With some further debugging I found that the interally used function `boost::this_thread::interruptible_wait()` takes too long, and its implementation makes clear where the origin is:  
https://github.com/boostorg/thread/blob/4abafccff4bdeb4b5ac516ff0c2bc7c0dad8bafb/src/win32/thread.cpp#L644-L652  
  
The `tolerable` is used by Windows to save energy using timer coalescing, and my interpretation is that - as our thread is doing nothing else that would create events - Windows waits the full `tolerable` time until it fires the timer.  
  
Although there is no guarantee that the timed operations wake the thread up at a specific timepoint, in my opinion it is wrong to produce a systematically too late wake-up. The `tolerable` should me much smaller (like 100ms max), even if I want to wait for an hour.

---

## Comment 1

> Username: Dani-Hub  
> Created at: 2021-07-10 13:19:40 UTC  
> Url: https://github.com/boostorg/thread/issues/348#issuecomment-877636843  

The discussion in https://stackoverflow.com/questions/30381866/boost-thread-wakes-up-too-late-in-1-58 also seems to indicate that a general relative tolerance (of 5% as in this case) might be questionable and should presumably be replaced by either an 32 ms  or 100 ms absolute tolerance.

---

## Comment 2

> Username: Dani-Hub  
> Created at: 2021-07-30 16:09:16 UTC  
> Url: https://github.com/boostorg/thread/issues/348#issuecomment-889994521  

@pdimov  Could please help me understanding some of the failures of the automatic [AppVeyor builds](https://ci.appveyor.com/project/pdimov/thread/builds/40182096)? I don't see any relation to my actual pull request, but maybe I'm misreading - Thanks!

---

## Comment 3

> Username: pdimov  
> Created at: 2021-08-01 07:23:35 UTC  
> Url: https://github.com/boostorg/thread/issues/348#issuecomment-890467134  

The MSVC tests fail spuriously because of hard-coded timeouts - they assume some blocking action needs to take less than some set number of milliseconds, and on Appveyor actions sometimes take more. I don't know enough about either Thread or the tests to be able to fix this.  
  
The GCC 5 failure (GCC 5 defaults to C++03) seems genuine, but I don't know when it was introduced. I don't remember seeing it before.  
  
Neither of these seems related to your PR.

---

## Comment 4

> Username: Dani-Hub  
> Created at: 2021-08-16 15:00:54 UTC  
> Url: https://github.com/boostorg/thread/issues/348#issuecomment-899581543  

@pdimov : Do you have any opinion on the PULL request? Are there any improvements that you would like to be applied?

---

## Comment 5

> Username: pdimov  
> Created at: 2021-08-16 15:16:51 UTC  
> Url: https://github.com/boostorg/thread/issues/348#issuecomment-899594163  

I can't offer any insight here, sorry. Maybe @ned14 could look at it. Looking at the SO thread, people seem to have good arguments against relative extension by 5%, and his counter was that Windows does not actually extend by 5% even if asked, but this seems to be wrong - Windows does extend by 5%.

---

## Comment 6

> Username: ned14  
> Created at: 2021-08-16 15:32:07 UTC  
> Url: https://github.com/boostorg/thread/issues/348#issuecomment-899605405  

Windows does change its behaviour over time. It's possible it used to previously ignore long timer slop times, but now honours them. This would not be the first time they've changed stuff here.  
  
I've no objection to capping the 5% slop to something reasonable, like five seconds. I wouldn't go less though, people need to stop assuming timers fire exactly when you've asked for them on non-realtime OSs.

---

## Comment 7

> Username: pdimov  
> Created at: 2021-08-16 15:38:19 UTC  
> Url: https://github.com/boostorg/thread/issues/348#issuecomment-899609678  

One second seems a better compromise. Extending this to five seconds doesn't seem to offer any real benefits.

---

## Comment 8

> Username: BrukerJWD  
> Created at: 2021-08-17 08:54:23 UTC  
> Url: https://github.com/boostorg/thread/issues/348#issuecomment-900117232  

One second is also fine for me :+1:

---

## Comment 9

> Username: ned14  
> Created at: 2021-08-17 10:47:23 UTC  
> Url: https://github.com/boostorg/thread/issues/348#issuecomment-900190108  

I can live with one second :)  
  
If you adjust your PR appropriately, I will review it. I'm not sure if I still have commit privs for thread, but if not, @pdimov certainly does.

---

## Comment 10

> Username: Dani-Hub  
> Created at: 2021-08-17 11:03:38 UTC  
> Url: https://github.com/boostorg/thread/issues/348#issuecomment-900200128  

Yes, my plan is to update the PULL request. I just wanted to give people a bit of time to respond. That seems to have happened now.

---

## Comment 11

> Username: pdimov  
> Created at: 2021-08-18 21:40:04 UTC  
> Url: https://github.com/boostorg/thread/issues/348#issuecomment-901447987  

> I'm not sure if I still have commit privs for thread  
  
You don't, but I can merge the PR (#356) on your behalf after you review it.

---

## Comment 12

> Username: Dani-Hub  
> Created at: 2021-08-27 16:49:34 UTC  
> Url: https://github.com/boostorg/thread/issues/348#issuecomment-907337083  

@ned14 This is a friendly reminder: Could you please review the last PR (#356)? Thanks!

---

## Comment 13

> Username: ned14  
> Created at: 2021-08-27 17:11:10 UTC  
> Url: https://github.com/boostorg/thread/issues/348#issuecomment-907349522  

Done.
