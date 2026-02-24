# #65 Implement pause support for RISC-V [Merged]

> Username: XieJiSS  
> Created at: 2023-10-24 03:04:24 UTC  
> Updated at: 2024-01-15 11:43:29 UTC  
> Merged at: 2024-01-15 11:43:19 UTC  
> Closed at: 2024-01-15 11:43:19 UTC  
> Url: https://github.com/boostorg/atomic/pull/65  

see-also: https://github.com/torvalds/linux/blob/84186fcb834ecc55604efaf383e17e6b5e9baa50/arch/riscv/include/asm/vdso/processor.h#L9

---

## Comment 1

> Username: XieJiSS  
> Created_at: 2023-10-24 03:40:22 UTC  
> Url: https://github.com/boostorg/atomic/pull/65#issuecomment-1776479580  

Note that there could be situations that the code is compiled with `-march=..._zihintpause` but get executed on a machine which does not support the hint. In such cases, `pause` (and `.4byte 0x100000F`) would be interpreted as `fence w, 0`. `fence w, 0` is `pause` for Zihintpause and `nop` for other machines, so this will not cause SIGILL.

---

## Review 2 [Commented]

> Username: Lastique  
> Created_at: 2024-01-14 20:24:07 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/atomic/pull/65#pullrequestreview-1820295758  

📁 include/boost/atomic/detail/pause.hpp

```diff
  54 |+ #elif defined(__riscv_muldiv)
  55 |+     /* In lieu of a halt instruction, induce a long-latency stall. */
  56 |+     __asm__ __volatile__("div %0, %0, zero" : "=r" (dummy));
```

> Username: Lastique  
> Created_at: 2024-01-14 20:24:07 UTC  
> Url: https://github.com/boostorg/atomic/pull/65#discussion_r1451797266  

`dummy` is not defined.  
  
I'm not sure a `div` is an appropriate replacement for a pause instruction. Assuming it keeps an execution unit occupied, it might affect the sibling thread progress. Wouldn't a series of `nop` instructions, or whatever it is called on RISC-V, be better?

> Username: XieJiSS  
> Created_at: 2024-01-15 00:38:51 UTC  
> Updated_at: 2024-01-15 00:39:45 UTC  
> Url: https://github.com/boostorg/atomic/pull/65#discussion_r1451837807  

Actually RISC-V RV32I/RV64I does not have a 'real' nop; the ISA aliased addi x0, x0, 0 to nop, which basically does the same as div (but addi pauses the cpu much shorter). Note that x0 is the zero register whose value is always zero. Here I'm not sure why the kernel developers decided to simulate a pause with div instruction instead off addi (or nop), IMO changing to nop is also feasible although we may need to issue 4 / 5 nops in a row to make the cpu pauses a bit longer?

> Username: Lastique  
> Created_at: 2024-01-15 01:19:30 UTC  
> Url: https://github.com/boostorg/atomic/pull/65#discussion_r1451847950  

I have next to no knowledge about RISC-V, so I can't tell. I imagine, this would also depend on the actual implementation of the architecture.  
  
FWIW, `pause` is supposed to allow the sibling thread to progress while we're waiting for something (either a mutex or a futex) in the short term, so it should be long enough to let a few instructions in the sibling thread to execute, and preferably not occupy execution units, if possible.  
  
Presumably, `div` has long latency and occupies integer division EU, which is supposedly rarely used, so maybe it is a reasonable choice from that perspective. My question was rather, is there a yet better choice? I.e. is there a guideline in RISC-V spec as to what instruction (or a sequence thereof) should be used for this purpose?

> Username: Lastique  
> Created_at: 2024-01-15 01:22:34 UTC  
> Url: https://github.com/boostorg/atomic/pull/65#discussion_r1451848820  

BTW, if I'm reading the `div` instruction correctly, it performs division by zero, right? Are there any side effects from that? E.g. a hardware exception or something?

> Username: Lastique  
> Created_at: 2024-01-15 01:38:49 UTC  
> Updated_at: 2024-01-15 01:38:50 UTC  
> Url: https://github.com/boostorg/atomic/pull/65#discussion_r1451853624  

The Linux kernel code you referenced is not equivalent to what you proposed in this PR. In Linux, `div` is emitted *in addition* to `pause`, presumably as a way to extend the duration of the pause. The actual pause hint is just the `pause` instruction, which, I think, is available unconditionally. I think, this `div` branch can be simply removed.

> Username: XieJiSS  
> Created_at: 2024-01-15 01:53:48 UTC  
> Updated_at: 2024-01-15 01:53:49 UTC  
> Url: https://github.com/boostorg/atomic/pull/65#discussion_r1451862046  

> BTW, if I'm reading the `div` instruction correctly, it performs division by zero, right? Are there any side effects from that? E.g. a hardware exception or something?  
  
That's a good point. However we don't need to worry about this, because I re-checked the ISA and it says that division by zero will not result to a trap or exception or so

> Username: XieJiSS  
> Created_at: 2024-01-15 02:05:41 UTC  
> Url: https://github.com/boostorg/atomic/pull/65#discussion_r1451866991  

> The Linux kernel code you referenced is not equivalent to what you proposed in this PR. In Linux, `div` is emitted _in addition_ to `pause`, presumably as a way to extend the duration of the pause. The actual pause hint is just the `pause` instruction, which, I think, is available unconditionally. I think, this `div` branch can be simply removed.  
  
Yeah, this is different from the kernel's logic, because according to the docs 'PAUSE' hint is used to tell the cpu to relax for a while, however issuing a div is nowhere near 'relax'. So I modified the code, such that the fast path (on cpu supporting zihintpause) will not execute that div instruction.  
  
And I spent some time digging the kernel history as well as related discussions in other repos, and I found this:   
![IMG_20240115_095759_836](https://github.com/boostorg/atomic/assets/24671280/8db5355b-c1de-4ae7-94d3-d9cc9470efb5)  
  
But if we replace div to add, then this add will basically have the same effect as .insn pseudo instrcution. So I believe we can eliminate this div  
  
I'll push a new commit regarding this ASAP


---

## Comment 3

> Username: Lastique  
> Created_at: 2024-01-15 11:43:29 UTC  
> Url: https://github.com/boostorg/atomic/pull/65#issuecomment-1892009702  

Thank you.

---
