# #36 - Consider not using _mm_mfence even when it is available [Closed]

> Username: AlexGuteniev  
> Created at: 2020-06-09 13:17:05 UTC  
> Updated at: 2020-06-11 20:57:30 UTC  
> Closed at: 2020-06-11 19:43:25 UTC  
> Url: https://github.com/boostorg/atomic/issues/36  

Consider either making `BOOST_ATOMIC_NO_MFENCE` overriding detection of SSE2 or x64, or even always defaulting to not using `mfence`.  
  
The reason is that `mfence` is slower than a dummy interlocked operation.  
  
On x64 there's even an intrinsic for that - see [__faststorefence](https://docs.microsoft.com/en-us/cpp/intrinsics/faststorefence?view=vs-2019)  
  
There might be a reason to use precisely `mfence`. An interlocked operation does not provide fences to non-temporal stores. But this is out of scope for implementing C++ memory model on x86 in the usual way.

---

## Comment 1

> Username: Lastique  
> Created at: 2020-06-09 14:18:10 UTC  
> Url: https://github.com/boostorg/atomic/issues/36#issuecomment-641327739  

If my reading of the C++ standard ([intro.races]/9) is correct, a release operation on an atomic should make all prior memory accesses visible to other threads after they execute an acquire operation on the atomic. The standard doesn't discriminate between regular or non-temporal stores, but it can be argued that it doesn't have to because both are "evaluations", in the standard's language. Put simply, non-temporal or not, the stores before the release fence should probably be flushed.  
  
I know Boost.Atomic is issuing `mfence` for seq_cst, not just release. But this probably means I may have to upgrade release fence to `sfence`.  
  
As for `__faststorefence`, I can't comment on that as I don't know which instructions it translates to. There may be a place for a lighter fence in Boost.Atomic as an extension, if it is generic enough and could be useful beyond just x86.

---

## Comment 2

> Username: Lastique  
> Created at: 2020-06-09 14:34:14 UTC  
> Url: https://github.com/boostorg/atomic/issues/36#issuecomment-641337823  

OTOH, all compilers seem to generate `mfence` for seq_cst and no instructions for other cases, see: https://gcc.godbolt.org/z/P6gF4g.

---

## Comment 3

> Username: AlexGuteniev  
> Created at: 2020-06-09 14:34:41 UTC  
> Url: https://github.com/boostorg/atomic/issues/36#issuecomment-641338119  

`__faststorefence` means `lock or [rsp], 0`  
A dummy interlocked operation.   
  
The particular choice of an interlocked operation is a matter of micro-optimization, like trying to use as fewer registers as possible (including result register), as fewer flags as possible, avoid stack variable, etc. Specifically this one avoids stack variable. Can be approximately simulated by `_InterlockedOr(reinterpret_cast<long volatile *>(_AddressOfReturnAddress()), 0)`  
  
So it is implementable only on x86 (32 and 64 bit), and actually exists as intrinsic only on 64-bit x86.  
  
> I know Boost.Atomic is issuing `mfence` for seq_cst, not just release. But this probably means I may have to upgrade release fence to `sfence`.  
  
No, I don't want that. The standard does not define non-temporal stores, and they are always intrinsics, so I don't think these should be covered.

---

## Comment 4

> Username: AlexGuteniev  
> Created at: 2020-06-09 14:36:29 UTC  
> Url: https://github.com/boostorg/atomic/issues/36#issuecomment-641339234  

https://stackoverflow.com/a/61382843/2945027 relevant SO thread  
https://github.com/microsoft/STL/pull/740 relevant MSVC STL PR

---

## Comment 5

> Username: AlexGuteniev  
> Created at: 2020-06-09 14:46:55 UTC  
> Url: https://github.com/boostorg/atomic/issues/36#issuecomment-641345700  

I wouldn't mind if you prefer keeping `mfence` for `seq_cst`, but adding `sfence` for `release` may turn me away from Boost.Atomic. If you're inclined to do that, at least please consider a macro to control it.

---

## Comment 6

> Username: Lastique  
> Created at: 2020-06-09 14:52:13 UTC  
> Url: https://github.com/boostorg/atomic/issues/36#issuecomment-641349048  

Given that other implementations don't issue `sfence`, I won't be doing that either. I guess, everyone is considering non-temporal stores an exception, so `__faststorefence` or an equivalent might be acceptable.

---

## Comment 7

> Username: AlexGuteniev  
> Created at: 2020-06-10 05:25:33 UTC  
> Url: https://github.com/boostorg/atomic/issues/36#issuecomment-641729872  

The most weird about emitting `mfence` for `atomic_thread_fence(seq_cst)` is that it is not emitted for other seq_cst operations: https://gcc.godbolt.org/z/JyPPxw  
```C++  
void inc_seq_cst(int* v)  
{  
    __atomic_fetch_add(v, 1, __ATOMIC_SEQ_CST);  
}  
```  
is no different from others.  
  
Similarly, `release` `store` does not have extra fences as well.  
So NT stores don't get any special care of by the memory model implementation.  
  
Maybe compiler developers hope that `mfence` will be faster with future CPU (faster than an interlocked op), or just missed this optimization opportunity. I've also read that `mfence` is _that_ much slow on Skylake after microcode update, not from the very beginning

---

## Comment 8

> Username: AlexGuteniev  
> Created at: 2020-06-11 20:23:34 UTC  
> Url: https://github.com/boostorg/atomic/issues/36#issuecomment-642908247  

Thanks.  
  
Especially for the link against `__faststorefence`.  
So trying to save a stack variable turns out to be a bad idea.  
(Though it is hypothetically possible that `__faststorefence` could be trained to avoid that, but I would not even bother looking into it)  
  
Then probably the best fence one can do is `lock not [dummy_variable]`, which does not try to save a variable, but saves registers, flags, and has a short encoding. This was suggested during MSVC change discussion, but unfortunately there's (currently) no intrinsic to emit it. (MSVC would use `lock inc [stack_variable]` emitted by `_InterlockedIncrement`)

---

## Comment 9

> Username: Lastique  
> Created at: 2020-06-11 20:57:30 UTC  
> Url: https://github.com/boostorg/atomic/issues/36#issuecomment-642924440  

Thanks, `lock not` indeed seems to be a better alternative.
