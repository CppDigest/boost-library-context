# #8 - gcc x86 dcas assembly issue [Closed]

> Username: zhenlw  
> Created at: 2017-01-08 03:51:25 UTC  
> Updated at: 2017-01-08 15:18:20 UTC  
> Closed at: 2017-01-08 15:18:20 UTC  
> Url: https://github.com/boostorg/atomic/issues/8  

We ran into this crash issue when using x86 32bit boost + gcc 4.6 (-O2 or higher).  
  
I think the part is buggy, in atomic/include/boost/atomic/detail/ops_gcc_x86_dcas.hpp:  
  
            uint32_t scratch;  
            __asm__ __volatile__  
            (  
                "movl %%ebx, %[scratch]\n\t"  
                "movl %[value_lo], %%ebx\n\t"  
                "movl %[dest], %%eax\n\t"  
                "movl 4+%[dest], %%edx\n\t"  
                ".align 16\n\t"  
                "1: lock; cmpxchg8b %[dest]\n\t"  
                "jne 1b\n\t"  
                "movl %[scratch], %%ebx\n\t"  
                : [scratch] "=m" (scratch), [dest] "=o" (storage)  
                : [value_lo] "a" ((uint32_t)v), "c" ((uint32_t)(v >> 32))  
                : BOOST_ATOMIC_DETAIL_ASM_CLOBBER_CC_COMMA "edx", "memory"  
            );  
  
Problem is that eax is touched, but not clobbered: We need to put it as a dummy output to tell the compiler that, otherwise the compiler may reuse eax and taking it as not changed.  
  
Actually the case in you lockfree::queue::initialization function will trigger this bug in some situations.

---

## Comment 1

> Username: Lastique  
> Created at: 2017-01-08 15:18:20 UTC  
> Url: https://github.com/boostorg/atomic/issues/8#issuecomment-271157193  

Fixed in a67cc1b055cf09f371e2eca544884634a1ccc886. Thanks for the report.
