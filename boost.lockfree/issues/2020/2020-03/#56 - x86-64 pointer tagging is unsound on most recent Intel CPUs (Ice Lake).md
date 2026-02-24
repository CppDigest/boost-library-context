# #56 - x86-64 pointer tagging is unsound on most recent Intel CPUs (Ice Lake) [Open]

> Username: oliver-giersch  
> Created at: 2020-03-26 10:53:48 UTC  
> Updated at: 2020-04-18 15:57:10 UTC  
> Url: https://github.com/boostorg/lockfree/issues/56  

According to the boost::lockfree [documentation](https://www.boost.org/doc/libs/1_72_0/doc/html/lockfree/rationale.html) and from what I can see from boost/lockfree/detail/tagged_ptr_ptrcompression.hpp, the implementation of the free-list stores a 16-bit tag value in the upper bits of a 64-bit pointer.  
  
Most recent Intel x86_48 microarchs (Ice Lake) use 5-level page tables and 57-bit virtual addresses, meaning this technique of pointer tagging can fail unexpectedly when used on such a processor.

---

## Comment 1

> Username: timblechmann  
> Created at: 2020-04-18 15:57:10 UTC  
> Url: https://github.com/boostorg/lockfree/issues/56#issuecomment-615894171  

hmm, this is a good point. it's a little unfortunate that `cmpxchg16b` does not exist on early AMD processors. though it seems that even the win10 minimum requirements state that it requires this instruction.  
  
though i'm wondering if the 57-bit virtual address space is enabled by simply running on those CPUs, or if it will require OS support to enable
