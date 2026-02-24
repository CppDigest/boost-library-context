# #50 extra_ops.hpp: adapt to AIX assembler [Closed]

> Username: Helflym  
> Created at: 2021-10-27 14:27:13 UTC  
> Updated at: 2021-10-28 15:17:32 UTC  
> Closed at: 2021-10-28 15:17:31 UTC  
> Url: https://github.com/boostorg/atomic/pull/50  

AIX assembler doesn't allow "bne- 1b" to jump to the previous label "1".  
It must be replaced by "bne- $N", N being the memory offset between  
the current instruction and the targeted label.

---

## Review 1 [Commented]

> Username: Lastique  
> Created_at: 2021-10-27 14:40:54 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/atomic/pull/50#pullrequestreview-790744286  

📁 include/boost/atomic/detail/extra_ops_gcc_ppc.hpp

```diff
 724 |-             "bne- 1b\n\t"
 906 |+ #ifndef _AIX
 907 |+             "bne- -1b\n\t"
```

> Username: Lastique  
> Created_at: 2021-10-27 14:40:54 UTC  
> Url: https://github.com/boostorg/atomic/pull/50#discussion_r737541893  

Is this change intentional?

> Username: Helflym  
> Created_at: 2021-10-27 14:45:13 UTC  
> Url: https://github.com/boostorg/atomic/pull/50#discussion_r737546444  

Not at all, it's a missclick I guess. I'll fix it.


---

## Comment 2

> Username: Lastique  
> Created_at: 2021-10-27 14:44:41 UTC  
> Url: https://github.com/boostorg/atomic/pull/50#issuecomment-953002437  

As a general comment, I would prefer to avoid `#ifdef`s all over the code and instead use macros to expand to either labels or offsets.

---

## Comment 3

> Username: Helflym  
> Created_at: 2021-10-27 14:48:16 UTC  
> Url: https://github.com/boostorg/atomic/pull/50#issuecomment-953005624  

Yes, that would be better indeed. I see if I can do that.   
By the way, do you know why I don't need to modify "core_arch_ops" header too ? It doesn't seem to be used by "atomic" tests on AIX, but I'm not sure why.

---

## Comment 4

> Username: Lastique  
> Created_at: 2021-10-27 14:56:20 UTC  
> Url: https://github.com/boostorg/atomic/pull/50#issuecomment-953013290  

> By the way, do you know why I don't need to modify "core_arch_ops" header too ?  
  
I suspect, your compiler supports `__atomic` intrinsics, so `core_ops_gcc_atomic.hpp` is implementing the atomic operations. For completeness, `core_arch_ops_gcc_ppc.hpp` needs to be updated too.

---

## Comment 5

> Username: Lastique  
> Created_at: 2021-10-27 15:00:40 UTC  
> Updated_at: 2021-10-27 15:01:45 UTC  
> Url: https://github.com/boostorg/atomic/pull/50#issuecomment-953017358  

I have something like this in mind:  
  
```  
#if !defined(_AIX)  
#define BOOST_ATOMIC_DETAIL_PPC_ASM_LABEL(label) label ":\n\t"  
#define BOOST_ATOMIC_DETAIL_PPC_ASM_JUMP(insn, label, offset) insn " " label "\n\t"  
#else  
#define BOOST_ATOMIC_DETAIL_PPC_ASM_LABEL(label)  
#define BOOST_ATOMIC_DETAIL_PPC_ASM_JUMP(insn, label, offset) insn " " offset "\n\t"  
#endif  
```  
  
And then use these macros in both headers.

---

## Comment 6

> Username: Lastique  
> Created_at: 2021-10-27 15:04:52 UTC  
> Url: https://github.com/boostorg/atomic/pull/50#issuecomment-953021536  

Also, I suppose, `_AIX` indicates AIX OS. Is there a macro specific to your compiler? I mean, if there is a standard gcc that supports labels on AIX, it would be better to enable labels in the code.

---

## Comment 7

> Username: Helflym  
> Created_at: 2021-10-27 15:14:00 UTC  
> Url: https://github.com/boostorg/atomic/pull/50#issuecomment-953030223  

The problem isn't related to the compiler but the assembler being used behind. I'm currently working with gcc-10, which is supporting labels. But it's calling AIX assembler instead of GNU as, and as far as I know it doesn't allow the "1b","1f" syntax.  I'm not sure any macro is able to detect that.

---

## Comment 8

> Username: Lastique  
> Created_at: 2021-10-28 15:17:31 UTC  
> Url: https://github.com/boostorg/atomic/pull/50#issuecomment-953947589  

Thanks, merged.

---
