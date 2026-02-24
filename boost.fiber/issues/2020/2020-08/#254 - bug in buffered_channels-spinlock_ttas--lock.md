# #254 - bug in buffered_channels/spinlock_ttas::lock? [Closed]

> Username: dixlorenz  
> Created at: 2020-08-14 09:31:32 UTC  
> Updated at: 2020-08-18 10:52:38 UTC  
> Closed at: 2020-08-18 05:43:46 UTC  
> Url: https://github.com/boostorg/fiber/issues/254  

I have quite a few fibers pushing items rapidly on a buffered_channel and every so often the program hangs with all cores being busy. They are all stuck at pushing into the channel, getting the lock, namely at   
  
std::this_thread::sleep_for( us0);  
  
That branch never increases the variable "retries", so it never gets to the third branch of the locking loop (where it would yield). I am pretty sure that is a bug.  
  
But also: why are both of these branches sleeping/yielding this_thread? Shouldn't they be sleeping/yielding this_fiber? I am unsure about this one, but as far as I understand it should give other fibers on the same thread the chance to release the lock.

---

## Comment 1

> Username: dixlorenz  
> Created at: 2020-08-17 18:24:28 UTC  
> Url: https://github.com/boostorg/fiber/issues/254#issuecomment-675038460  

I added a pull request (#255) to increase the variable; my problem does not occur anymore

---

## Comment 2

> Username: olk  
> Created at: 2020-08-18 05:43:46 UTC  
> Url: https://github.com/boostorg/fiber/issues/254#issuecomment-675264991  

ty

---

## Comment 3

> Username: dixlorenz  
> Created at: 2020-08-18 10:52:38 UTC  
> Url: https://github.com/boostorg/fiber/issues/254#issuecomment-675408705  

I spoke too soon, my code still hangs, just more rarely.  
  
Unfortunately I haven't been able to reproduce it in a small code sample; I can't even reproduce it on all the machines I have available, only on the one with the fewest CPUs (4 + HT). I am using the regular round-robin scheduler and I have one thread per CPU Core. Each of those threads has (among others, but those should be dormant) one fiber pushing items onto a buffered_channel; one thread also has a fiber popping items. Sometimes it hangs, all threads are stuck in spinlock_ttas::lock(), after my fix now in line 78, std::this_thread::yield(). I have only witnessed the hang with very high contention where the actual processing behind the items was very short.  
  
The only explanation I have is that the one fiber that has the lock is not active atm and this_thread::yield() doesn't cycle through available fibers. But that would mean that the fiber owning the lock has switched out and I don't see any code in buffered_channel::push or buffered_channel::pop that activates another context without also releasing the lock (but then, "std::memory_order_release" et al are way outside of my comfort zone). I've seen the hang with a full channel or an almost empty channel. There is no closing of the channel or anything, it hangs sometimes in the middle.  
  
I am still trying to get a reproducible case, but I am reasonably sure that there is a bug hidden somewhere buffered_channel::push, buffered_channel::pop and spinlock_ttas::lock.
