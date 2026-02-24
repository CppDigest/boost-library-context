# #53 - [question] risc-v support [Closed]

> Username: XieJiSS  
> Created at: 2021-11-29 10:41:59 UTC  
> Updated at: 2021-11-30 11:00:14 UTC  
> Closed at: 2021-11-29 11:29:01 UTC  
> Url: https://github.com/boostorg/atomic/issues/53  

Greetings,  
  
Is there any plan for making `boost/atomic` compatible with RISC-V? This will be useful when trying to Implement lock-free data structures.  
  
ref https://github.com/sfztools/sfizz/issues/1023#issuecomment-967231108

---

## Comment 1

> Username: Lastique  
> Created at: 2021-11-29 11:29:00 UTC  
> Url: https://github.com/boostorg/atomic/issues/53#issuecomment-981548144  

Modern compilers provide intrinsics, which Boost.Atomic already use. If your compiler supports RISC-V then it should already be supported by Boost.Atomic.  
  
There are a number of extended operations that are provided by Boost.Atomic and not directly covered by intrinsics. Those operations are emulated through intrinsics where a more specialized implementation is not present, which is the case for RISC-V.   
  
Currently, there are no plans to add a specialized assembler backend for RISC-V. I have no knowledge of RISC-V and no hardware to test.

---

## Comment 2

> Username: XieJiSS  
> Created at: 2021-11-29 17:24:08 UTC  
> Url: https://github.com/boostorg/atomic/issues/53#issuecomment-981849735  

Thanks for the explanation.

---

## Comment 3

> Username: XieJiSS  
> Created at: 2021-11-30 07:24:05 UTC  
> Updated at: 2021-11-30 08:00:14 UTC  
> Url: https://github.com/boostorg/atomic/issues/53#issuecomment-982354748  

Hi @Lastique , if specifically, I want to patch `include/boost/atomic/detail/pause.hpp` locally, what might be the alternative for i386 `pause` and arm `yield`? Currently in RISC-V, we may only choose `nop` (since `pause` instruction is still in discussion), and I'm not sure whether it is ok to do so. Will this brings possible speed regression (which is acceptable in my use case), or completely breaks the `void pause()` function? Thanks!  
  
EDIT: In [this article](https://coderedirect.com/questions/158725/how-does-x86-pause-instruction-work-in-spinlock-and-can-it-be-used-in-other-sc), I've read that older x86 CPUs will explain `pause` as `rep nop`. Also, in linux kernel (`linux/arch/...`), I've found that they use `nop; nop; nop; nop; nop; nop; nop; nop; nop; nop;` to implement a `yield` (*i.e.* i386 `pause`). Does this mean that I need to use multiple `nop`s if `pause` is not available?  
  
https://github.com/torvalds/linux/blob/d58071a8a76d779eedab38033ae4c821c30295a5/arch/arm/include/asm/vdso/processor.h#L11-L15

---

## Comment 4

> Username: Lastique  
> Created at: 2021-11-30 10:52:41 UTC  
> Updated at: 2021-11-30 11:00:14 UTC  
> Url: https://github.com/boostorg/atomic/issues/53#issuecomment-982519560  

The `pause()` function is used to suspend current thread execution for some time without yielding to the OS kernel, which is useful in spin loops. The `pause` instruction in x86 is suspending execution on the current virtual core so that the sibling virtual core can proceed its execution faster (as it reduces contention for the execution units in the physical core). The `yield` instruction on ARM is also suspending execution of the current core for some amount of cycles. In both cases, the instruction also reduces power consumption, which lets CPU clocks to stay high.  
  
I don't know if there is an instruction in RISC-V that has a similar effect to that of `pause` and `yield`. If there is, it should be used to implement `pause()`. If not, `pause()` should be left empty (which is how it is now).  
  
Note that `nop` instructions are not equivalent to `pause` or `yield`. This instruction is typically used to align code, and CPUs are optimized to dispatch `nop` as fast as possible, so using `nop` would not suspend the thread for long enough. It would also not reduce power consumption much since the CPU would be still decoding `nop` and other instructions at full tilt.
