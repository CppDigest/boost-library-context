# #43 - Consider removing the AARCH64 128 bit atomic implementation (LDXP+STXP loop) [Closed]

> Username: pgroke-dt  
> Created at: 2021-01-15 13:29:33 UTC  
> Updated at: 2021-01-15 16:42:37 UTC  
> Closed at: 2021-01-15 15:08:41 UTC  
> Url: https://github.com/boostorg/atomic/issues/43  

Using an `LDXP`+`STXP` loop for 128 bit atomics on ARM has multiple (IMO significant) downsides - at least when combined with the claim `BOOST_ATOMIC_INT128_LOCK_FREE == 2`.  
  
1) Loads cause a write. While this write is guaranteed to never change the value, it's still a write and thus may be problematic in e.g. shared memory scenarios where one of the processes has the memory mapped as read-only.  
  
2) Loads cause the cache line to be invalidated for all other cores. At least for me, when I write code that uses atomics, I expect an atomic load to have negligible impact on other threads access the same variable. I certainly don't expect the atomic load to invalidate the cache line for all other participants.  
  
3) Plain loads and stores both have to use a retry loop which means they can potentially take a very long time. IMO also unexpected and undesirable.  
  
Personally I'd do one of the following:  
* Keep the implementation but make it opt-in via some `#define` the user must set (preferred)  
* Keep the implementation but make it opt-out via some `#define` the user can set  
* Keep the implementation but don't claim `BOOST_ATOMIC_INT128_LOCK_FREE == 2`  
  
I'm aware that using the emulation via the lock pool has even more downsides. My main concern is that with the current implementation, it looks like there's a "normal", unproblematic hardware implementation for 128 bit atomics, when this hardware implementation does have some IMO significant and unexpected downsides.  
  
For context: we're currently using Boost.Atomic without linking against the library. This means everything that would require emulation automatically fails to link. We want this, because it means we can use atomics without having to consider the implications of emulated atomics. Atomics just are ... atomic :)  
Having the LDXP+STXP loop implementation on AARCH64 somewhat muddles the waters there.

---

## Comment 1

> Username: Lastique  
> Created at: 2021-01-15 15:08:41 UTC  
> Updated at: 2021-01-15 15:15:49 UTC  
> Url: https://github.com/boostorg/atomic/issues/43#issuecomment-760996197  

1. Loads can cause a store with Boost.Atomic, this is a documented allowed behavior. It is also the case on x86, for example. Atomics in read-only memory are not supported by Boost.Atomic.  
2. This seems to be an unfortunate consequence of a store, which is used to implement the load. I consider this a performance limitation of the target architecture. Double-width atomics are commonly more expensive than single-width, so this effect is not really surprising. It is certainly not the reason to simply not support the operation at all.  
3. I'm not sure what you mean here, could you elaborate? If you mean that atomic operations are implemented with a loop then that is not specific to 128-bit loads and stores - almost any atomic operation on an LL/SC architecture must be implemented with a loop, and quite a few operations on x86 are also implemented with a loop. The situation when lots of threads hammer the same atomic object with operations and prevent each other from progressing is theoretically possible, but in that case you should probably reconsider user's program design to reduce contention.  
  
All in all, I do not think that disabling 128-bit atomic operations would be better than not, even with all their performance penalties. If you are concerned with performance of atomic operations on a given architecture then you should probably just not use `atomic` in that case and use e.g. a mutex. This would be better than using a lock pool. People who do use `atomic`, including with 128-bit types, want exactly that - a lock-free atomic, if possible. The lock pool in this case is just an undesirable fallback, when lock-free implementation is not possible. So undesirable, in fact, that I would recommend testing if lock-free atomic is supported using macros and using a dedicated mutex if not.

---

## Comment 2

> Username: pgroke-dt  
> Created at: 2021-01-15 16:00:47 UTC  
> Url: https://github.com/boostorg/atomic/issues/43#issuecomment-761027431  

Ad 3: I know that atomic operations that require a read-modify-write cycle often require a loop. What I haven't seen so far is a plain atomic load or plain atomic store that requires a loop. Which doesn't mean that have to be totally uncommon - it just means I haven't seen it so far :) I just _assumed_ that it was uncommon.  
  
If you know it by heart, I'd appreciate if you could let me know which other platforms there are where a plain atomic load is implemented via either a loop or a construct that will invalidate the cache line on all other cores.

---

## Comment 3

> Username: Lastique  
> Created at: 2021-01-15 16:27:49 UTC  
> Updated at: 2021-01-15 16:42:37 UTC  
> Url: https://github.com/boostorg/atomic/issues/43#issuecomment-761042757  

> What I haven't seen so far is a plain atomic load or plain atomic store that requires a loop. Which doesn't mean that have to be totally uncommon - it just means I haven't seen it so far :) I just assumed that it was uncommon.  
  
In a sense, it is uncommon. Double-width atomics are uncommon on their own, and not all CPU architectures support them. Single-width atomics are usually less expensive and don't require jumping through hoops. Most architectures guarantee atomicity of suitably aligned regular loads and stores or provide dedicated instructions for that.  
  
> If you know it by heart, I'd appreciate if you could let me know which other platforms there are where a plain atomic load is implemented via either a loop or a construct that will invalidate the cache line on all other cores.  
  
ARMv7/AArch32 and AArch64 must implement double-width stores in a loop. You've already discovered that on AArch64 double-width loads also require a loop.  
  
I mentioned x86 above. In case of double-width atomics, loads are implemented with `cmpxchg16b`, which issues a store to the memory location. Stores are also implemented with a loop. In 32-bit x86, this also applies if the atomic object is not aligned to 8 bytes (`cmpxchg8b` is used in that case).  
  
I can't remember others off the top of my head. But I didn't implement atomic operations for every CPU architecture.

---

## Comment 4

> Username: pgroke-dt  
> Created at: 2021-01-15 16:41:46 UTC  
> Url: https://github.com/boostorg/atomic/issues/43#issuecomment-761050581  

Thanks, much appreciated!  
The "CAS-for-read" is unfortunate but less scary to me than the AARCH64 LL/SC loop for a load.  
I still consider that kind-of a hack, but I do understand your reasons for wanting to keep it.
