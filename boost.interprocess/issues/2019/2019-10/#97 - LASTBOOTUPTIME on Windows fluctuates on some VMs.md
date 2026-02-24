# #97 - LASTBOOTUPTIME on Windows fluctuates on some VMs [Closed]

> Username: WilliamClements  
> Created at: 2019-10-03 17:12:34 UTC  
> Updated at: 2021-02-20 23:43:06 UTC  
> Closed at: 2021-02-20 23:43:06 UTC  
> Url: https://github.com/boostorg/interprocess/issues/97  

I saw Issue #96 (Getting bootup time on Windows is not stable) . That proposal may be useful.  There have been discussions on this stability theme over many years.   
  
I work with a premium desktop product (Autodesk Revit) . It employs boost interprocess in production. Long ago, we chose the boot time stamp strategy = BOOST_INTERPROCESS_BOOTSTAMP_IS_LASTBOOTUPTIME and we have been pretty happy with it.  
  
Yes, in the wild, mucking with the system time, or other randomness, invalidates it . In that case, our application refuses to start up. To us, systems administered with professional-level IT rarely exhibit that kind of problem. So that's good.  
  
New problem, though. We see it with a newly minted Windows 10 VM image. I won't go into a painful level of detail unless asked. When we start application, and get the last bootup time, via boost interprocess API, sometimes answer is off by exactly one hour! **Chaos ensues**. It has now been noticed that on these VMs, the system current _time zone_ synchronously fluctuates, but in a complementary way. This suggests a revised calculation may eliminate fluctuation problem. (Currently integrated with boost 1.68.0).   
  
Any advice or news on this?

---

## Comment 1

> Username: WilliamClements  
> Created at: 2019-10-31 16:49:12 UTC  
> Url: https://github.com/boostorg/interprocess/issues/97#issuecomment-548467218  

Update: duh if it fluctuates by one hour it is time-zone jitter. i.e. daylight savings time

---

## Comment 2

> Username: igaztanaga  
> Created at: 2019-11-24 17:21:08 UTC  
> Url: https://github.com/boostorg/interprocess/issues/97#issuecomment-557909080  

Maybe you can try with `BOOST_INTERPROCESS_BOOTSTAMP_IS_SESSION_MANAGER_BASED` macro.  
  
This alternative works only for Vista and later versions

---

## Comment 3

> Username: assarbad  
> Created at: 2020-05-11 15:26:19 UTC  
> Url: https://github.com/boostorg/interprocess/issues/97#issuecomment-626774129  

@igaztanaga the approach I just suggested over at #96 would also work, although it would require conversion to Unix epoch (which the event log method doesn't have to do). Quoting myself:  
  
> Still, it would be much more sensible to query the creation time of `\REGISTRY` or `\SystemRoot` in the Object Manager namespace. My tool [NT Objects](https://bitbucket.org/assarbad/ntobjx) demonstrates this. And yes, these can be queried as totally unprivileged user. I just verified it _after a reboot_ and I have the following time stamps (times in UTC):  
>   
> * `\REGISTRY`: 2020-05-11 14:29:45.644  
> * `\SystemRoot`: 2020-05-11 14:29:45.555  
>   
> (as a side-note, these values are actually roughly _nine seconds_ before the timestamp of the event 6005 "The Event log service was started.")  
>   
> I didn't check what [PSTools](https://docs.microsoft.com/en-us/sysinternals/downloads/pstools) (or `psinfo` in particular) use, but using event ID 6005 really doesn't strike me as a very prudent course of action.  
>   
> And the error being thrown is also rather generic for an error that could _reasonably_ occur weeks or months into a given boot session. And what's more, running a program with such a widely used library may succeed now, but after the event log cycles or I clear it, the program just starts to fail with a rather unspecific error message.  
>   
  
Since no project member commented on #96, I thought it might be worthwhile to draw your attention to this.  
  
The registry method should also be fairly robust, although it's arguably easier to tinker with than the object creation time within the NT Object Manager namespace.  
  
The charm of my proposed method would be, that it works ever since NT4, so no pre/post-Vista distinction. And since Boost seems to have committed to the event log based boot time detection, NT as a platform seems to be a prerequisite anyway.

---

## Comment 4

> Username: igaztanaga  
> Created at: 2020-06-02 22:45:40 UTC  
> Url: https://github.com/boostorg/interprocess/issues/97#issuecomment-637846646  

This is an open issue without no good fix until now, so any help is appreciated.  
  
@assarbad: Does your method work correctly when Fast Boot/hybrid shutdown is enabled (kernel is hibernated instead and many kernel parameters are not refreshed, just recovered from the previous session)? If yes, a patch or helping code would be a great help to avoid additional bugs when implementing something that you might have already implemented. Thanks!

---

## Comment 5

> Username: assarbad  
> Created at: 2020-06-03 19:17:51 UTC  
> Url: https://github.com/boostorg/interprocess/issues/97#issuecomment-638407410  

> @assarbad: Does your method work correctly when Fast Boot/hybrid shutdown is enabled (kernel is hibernated instead and many kernel parameters are not refreshed, just recovered from the previous session)? If yes, a patch or helping code would be a great help to avoid additional bugs when implementing something that you might have already implemented. Thanks!  
  
I guess whether it works depends entirely on what's the desired behavior in that case. Is the desired behavior to get a new name after a fast boot or not? Because technically this is not the same as a full boot and so I'd expect the boot time (object creation time) to remain constant.  
  
Please specify what's the desired behavior and I'll check if it matches the actual behavior of Windows.

---

## Comment 6

> Username: igaztanaga  
> Created at: 2020-06-03 22:33:22 UTC  
> Url: https://github.com/boostorg/interprocess/issues/97#issuecomment-638495972  

The desired behavior is that a shutdown and boot (either with FastBoost activated or not) has always the same efect (it's an optimization/implementation detail), it should get a new name and new fresh shared memory can be created.

---

## Comment 7

> Username: igaztanaga  
> Created at: 2021-02-20 23:43:06 UTC  
> Url: https://github.com/boostorg/interprocess/issues/97#issuecomment-782766408  

I've checked that \SystemRoot date is not updated when "FastBoost" option is set in Windows, so this is not a viable approach to obtain the bootstamp.  
  
The alternative is to use the BOOST_INTERPROCESS_BOOTSTAMP_IS_SESSION_MANAGER_BASED option, which is the default option since Boost 1.74.
