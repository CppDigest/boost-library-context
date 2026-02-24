# #120 - multiple problems with ppc32_sysv_elf [Closed]

> Username: kernigh  
> Created at: 2019-09-28 03:47:30 UTC  
> Updated at: 2019-10-23 09:19:56 UTC  
> Closed at: 2019-10-23 09:19:56 UTC  
> Url: https://github.com/boostorg/context/issues/120  

It is great that boost fcontext supports many platforms. There are multiple problems with ppc32_sysv_elf. This is the ABI of 32-bit PowerPC (like old Apple Macintosh) running Linux or BSD, but not Mac OS X. We are trying to run boost fcontext on OpenBSD/macppc. I thank Otto Moerboek, who reported [problems with ppc32_sysv_elf to openbsd-ports](https://marc.info/?l=openbsd-ports&m=156690907813347&w=2).  
  
There are 2 versions of this ABI:  
  
- Sun Microsystems, [_System V Application Binary Interface, PowerPC Processor Supplement_](https://refspecs.linuxbase.org/elf/elfspec_ppc.pdf), 1995.  
- [_Power Architecture 32-bit Application Binary Interface Supplement 1.0 - Linux & Embedded_](https://www.polyomino.org.uk/publications/2011/Power-Arch-32-bit-ABI-supp-1.0-Unified.pdf), 2011.  
  
The "Linux ABI" is really an updated System V ABI that adds more features like secure PLT and thread-local storage. When I write "System V ABI" or "ELF ABI", I refer to both versions of this ABI.  
  
Beware that [32-bit PowerPC Mac OS X has a different ABI](https://developer.apple.com/library/archive/documentation/DeveloperTools/Conceptual/LowLevelABI/100-32-bit_PowerPC_Function_Calling_Conventions/32bitPowerPC.html#//apple_ref/doc/uid/TP40002438-SW20). The *ppc32_sysv_macho* files in Boost are misnamed, because they should not use the System V calling convention. (Some other processors on Mac OS X do use the System V convention (but without ELF); 32-bit PowerPC is a special case where Mac OS X inherited a different ABI from classic Mac OS.)  
  
In the System V ABI, when the arguments to a function are pointers or 32-bit integers, then the caller passes the first 8 arguments to the callee in registers r3 to r10. If an argument is a struct, the caller passes a pointer to the struct.  
  
I divide this issue into 6 subproblems.  
  
**Subproblem 1: fcontext only works on Linux**  
  
Linux deviates from the System V ABI when returning a small struct (of up to 8 bytes). This is a problem, because some fcontext functions return [a transfer_t, a struct of 8 bytes](https://github.com/boostorg/context/blob/ff08ccbbfa6e4dd20dd6e550f3b15785097e34d9/include/boost/context/detail/fcontext.hpp#L25). The asm in Boost is for Linux and doesn't work on BSD. The difference between BSD and Linux is a secret, not a documented part of the ABI.  
  
_System V ABI_ (1994) says in "Return Values", page 3-22,  
  
> A structure or union whose size is less than or equal to 8 bytes shall be returned in **r3** and **r4**, as if it were first stored in an 8-byte aligned memory area and then the low-addressed word were loaded into **r3** and the high-addressed word into **r4**.  Bits beyond the last member of the structure or union are not defined.  
  
I don't know why, but Linux ignores this part of ABI, and returns any size of struct through a hidden pointer. The caller passes the hidden pointer in **r3**; this moves the first argument to **r4**. _Linux ABI_ (2011) updated the ABI to match what happens on Linux; but BSD systems still return small structs in **r3** and **r4**. By BSD, I mean at least NetBSD and OpenBSD; I haven't checked FreeBSD.  
  
(BSD deviates from the ABI in a different way: GCC returns the struct in the low bytes of r3 or of r3:r4, not the high bytes, so the undefined bits are before the first member, not "beyond the last member" of the struct. This doesn't matter for fcontext, because the transfer_t fills all 8 bytes of r3:r4.)  
  
Because of this difference, Linux and BSD have different calling conventions for jump_fcontext, ontop_fcontext, and the _fn_ argument of ontop_fcontext:  
  
```  
/* Linux */  
jump_fcontext(hidden transfer_t *r3, r4, r5);  
ontop_fcontext(hidden transfer_t *r3, r4, r5, r6)  
    where r6 has (*fn)(hidden transfer_t *r3, transfer_t *r4);  
  
/* BSD */  
transfer_t r3:r4 = jump_fcontext(r3, r4);  
transfer_t r3:r4 = ontop_fcontext(r3, r4, r5);  
    where r5 has transfer_t r3:r4 = (*fn)(transfer_t *r3);  
```  
  
The asm in Boost is for Linux. For example, [jump_fcontext uses the hidden pointer **r3**](https://github.com/boostorg/context/blob/ff08ccbbfa6e4dd20dd6e550f3b15785097e34d9/src/asm/jump_ppc32_sysv_elf_gas.S#L198):  
  
```  
    # return transfer_t   
    stw  %r6, 0(%r3)  
    stw  %r5, 4(%r3)  
```  
  
In BSD, the hidden pointer r3 doesn't exist. The function arguments are in different registers: r3 and r4 in BSD, but r4 and r5 in Linux.  
  
**Subproblem 2: ontop_fcontext passes the wrong hidden pointer**  
  
When a function receives the hidden pointer in r3, it must store its return value where r3 is pointing. If it stores its return value somewhere else, then the value is lost. The logic in ontop_fcontext to restore the hidden pointer is wrong, so the return value gets lost. The [prototype of ontop_fcontext](https://github.com/boostorg/context/blob/ff08ccbbfa6e4dd20dd6e550f3b15785097e34d9/include/boost/context/detail/fcontext.hpp#L37) is,  
  
```  
transfer_t ontop_fcontext( fcontext_t const to, void * vp, transfer_t (* fn)( transfer_t) );  
```  
  
Stack B calls ontop_fcontext to switch to Stack A, then make a tail call to _fn_ on top of Stack A, so _fn_ will return a transfer_t to Stack A. For this to work, ontop_fcontext must restore the hidden pointer of Stack A. The context of Stack A had [saved its hidden pointer r3](https://github.com/boostorg/context/blob/ff08ccbbfa6e4dd20dd6e550f3b15785097e34d9/include/boost/context/detail/fcontext.hpp#L25) using `stw %r3, 228(%r1) # save hidden`; so the obligation is to restore %r3 from 228(%r1).  
  
Instead, ontop_fcontext does `lwz %r4, 228(%r1) # restore hidden`, so Stack A's hidden pointer is now in r4, while Stack B's hidden pointer is still in r3. It then calls _fn_ with the wrong hidden pointer, so _fn_ will store its return value on the wrong stack. This will lose the return value.  
  
Some implementations of ontop_fcontext for other ABIs seem to make the same mistake, by passing the wrong hidden pointer. This mistake might spread as people port fcontext to more platforms.  
  
**Subproblem 3: ontop_fcontext borrows space from the other stack**  
  
Even if ontop_fcontext would pass the correct hidden pointer to _fn_, there is another problem. The call to `transfer_t (* fn)( transfer_t)` needs to allocate 8 bytes of memory for the transfer_t argument to _fn_, because the System V ABI passes the struct as a pointer to those 8 bytes. The problem is that ontop_fcontext makes a tail call to _fn_ without leaving a stack frame, so there is no stack frame for allocating the 8 bytes.  
  
For now, ontop_fcontext cheats by using the hidden pointer of the other Stack B. The call to _fn_ uses the hidden pointers from both stacks: the return value from _fn_ is on one stack, and the argument to _fn_ is on the other stack. This cheat has its own problem: _fn_ is an arbitrary function, and may resume Stack B, but Stack B will reuse its 8 bytes for something else. _fn_ can also just deallocate the entire Stack B. Either way, _fn_ may accidentally use a transfer_t after freeing it.  
  
For example, [context_exit](https://github.com/boostorg/context/blob/ff08ccbbfa6e4dd20dd6e550f3b15785097e34d9/include/boost/context/continuation_fcontext.hpp#L62) is a _fn_ that would deallocate Stack B:  
  
```  
template< typename Rec >  
transfer_t context_exit( transfer_t t) noexcept {  
    Rec * rec = static_cast< Rec * >( t.data);  
    // destroy context stack  
    rec->deallocate();  
    return { nullptr, nullptr };  
}  
...  
    // destroy context-stack of `this`context on next context  
    ontop_fcontext( t.fctx, rec, context_exit< Rec >);  
```  
  
The call to rec->deallocate() wrongly frees a transfer_t, either the return value or the argument. This problem is not easy to fix. One may modify ontop_fcontext to leave a stack frame in Stack A (between _fn_ and the function to be resumed), but this breaks C++ exceptions, because they don't know how to unwind the frame. This would cause a program to abort with "terminate called..." instead of catching the exception. Exceptions need to work, because [context_unwind](https://github.com/boostorg/context/blob/ff08ccbbfa6e4dd20dd6e550f3b15785097e34d9/include/boost/context/continuation_fcontext.hpp#L56) is a _fn_ for ontop_fcontext that throws an exception to [context_entry](https://github.com/boostorg/context/blob/ff08ccbbfa6e4dd20dd6e550f3b15785097e34d9/include/boost/context/continuation_fcontext.hpp#L81).  
  
**Subproblem 4: make_fcontext misaligns the stack pointer**  
  
The System V ABI requires a stack alignment of 16 bytes, and [make_fcontext tries to align the stack pointer](https://github.com/boostorg/context/blob/ff08ccbbfa6e4dd20dd6e550f3b15785097e34d9/src/asm/make_ppc32_sysv_elf_gas.S#L85), but forgets that 244 is not a multiple of 16. When we run the new stack, [jump_fcontext removes the 244-byte context](https://github.com/boostorg/context/blob/ff08ccbbfa6e4dd20dd6e550f3b15785097e34d9/include/boost/context/continuation_fcontext.hpp#L81), so the stack pointer becomes misaligned. The stack has only 4-byte alignment.  
  
Some programs won't notice the misalignment. Integers in 32-bit PowerPC only need 4-byte alignment (because a 4-byte word is the largest size for integer loads and stores). Double-precision floats prefer 8-byte alignment, and might be slightly slower with 4-byte alignment. There might be a problem with altivec vectors. Many programs don't use altivec (because OpenBSD supports PowerPC processors with and without altivec), but some assembly code might try to detect altivec and use it.  
  
**Subproblem 5: make_fcontext corrupts register r13**  
  
In the System V ABI, r13 must point to the executable's small data area. OpenBSD deviates from the ABI and fails to set r13, but [NetBSD sets r13 in crt0.S](http://cvsweb.netbsd.org/bsdweb.cgi/src/lib/csu/arch/powerpc/crt0.S?rev=1.7&content-type=text/x-cvsweb-markup&only_with_tag=MAIN).  
  
Boost fcontext saves and restores r13 in each context, which it should not do. make_fcontext does not fill in a value for r13, so when jump_fcontext runs the new stack, [it restores a garbage value to r13](https://github.com/boostorg/context/blob/ff08ccbbfa6e4dd20dd6e550f3b15785097e34d9/src/asm/jump_ppc32_sysv_elf_gas.S#L163). This mistake might come from confusion with the Mac ABI. One might need to save and restore r13 in the Mac ABI, but not in the System V ABI.  
  
In practice, compilers like gcc might not use r13, because they don't know whether the code is for an executable or a shared library. (An executable can find its small data in r13, but a shared library can't.) Code running on the new stack might not notice if r13 holds garbage.  
  
**Subproblem 6: context contains misaligned floats**  
  
jump_fcontext [saves registers f14 to f31 at offset 0](https://github.com/boostorg/context/blob/ff08ccbbfa6e4dd20dd6e550f3b15785097e34d9/src/asm/jump_ppc32_sysv_elf_gas.S#L85) in a 244-byte context. Each register holds an 8-byte double-precision float. The storage is misaligned because jump_fcontext subtracted 244 from the stack pointer, but 244 isn't a multiple of 8. This misalignment may cause a slight slowdown when switching contexts.  
  
The System V ABI uses 8-byte alignment for 8-byte floats, and the Mac ABI uses 4-byte alignment for 8-byte floats, but we don't need the ABI alignment, because the context is private to the asm code in fcontext. We may only consider the performance of _stfd_ and _lfd_ in PowerPC processors. My PowerBook G4, where I run OpenBSD/macppc, has an MPC7447A processor, described in Freescale's [_MPC7450 RISC Microprocessor Family Reference Manual_](https://www.nxp.com/docs/en/reference-manual/MPC7450UM.pdf), where section 2.3.3 says,  
  
> In general, floating-point word accesses should always be word-aligned and floating-point double-word accesses should always be double-word–aligned. Frequent use of misaligned accesses is discouraged because they can degrade overall performance.  
  
Because 244 is a multiple of 4, the 8-byte floats in the context, though misaligned, still have 4-byte alignment. This is enough to avoid an alignment exception (sections 2.3.3 and 4.1), but slower than an aligned access.  
  
There are patches for openbsd-ports that fix some of the above 6 subproblems, but I have not yet brought the patches to GitHub.

---

## Comment 1

> Username: olk  
> Created at: 2019-09-28 09:47:00 UTC  
> Url: https://github.com/boostorg/context/issues/120#issuecomment-536171094  

You are welcome to provide a pull request that implements the required assembler files for 32-bit PowerPC Mac OS X.  
I can't help you because I don't own a PPC32 MacOS X system.

---

## Comment 2

> Username: kernigh  
> Created at: 2019-10-04 02:22:46 UTC  
> Url: https://github.com/boostorg/context/issues/120#issuecomment-538200977  

Before I can try fixing OpenBSD on ppc32, I need 2 other changes for OpenBSD on any processor. I opened 2 pull requests:  
  
- https://github.com/boostorg/test/pull/231 to run tests on OpenBSD  
- https://github.com/boostorg/context/pull/121 to allocate stacks on OpenBSD  
  
Other ppc32 systems (like NetBSD and Linux) probably don't need these 2 changes.  
  
To properly fix subproblem 3 "ontop_fcontext borrows space from the other stack", I may need to write an .eh_frame in modified DWARF to unwind a stack frame. I don't know how to do this, but `gcc -S` might provide an example that I can adapt.

---

## Comment 3

> Username: olk  
> Created at: 2019-10-04 04:29:48 UTC  
> Url: https://github.com/boostorg/context/issues/120#issuecomment-538227025  

#121 has been merged

---

## Comment 4

> Username: kernigh  
> Created at: 2019-10-17 02:44:39 UTC  
> Url: https://github.com/boostorg/context/issues/120#issuecomment-542972434  

To fix subproblem 3, ontop_fcontext() for ppc32_sysv_elf must allocate a transfer_t before it calls fn(), and free the transfer_t after fn() returns, or when fn() throws a C++ exception. The hard part is handling an exception. I have 2 drafts:  
  
1. [branch kernigh-ppc32-1](https://github.com/kernigh/context/tree/kernigh-ppc32-1) uses an `.eh_frame` in assembly to free the transfer_t.  
2. [branch kernigh-ppc32-2](https://github.com/kernigh/context/tree/kernigh-ppc32-2) adds a new file `tail_ppc32_sysv_elf.cpp` and uses C++ to free the transfer_t.  
  
I prefer my 2nd draft, because the `.eh_frame` in my 1st draft might not work with all C++ compilers. The 2nd draft allows the C++ compiler to emit an `.eh_frame` or equivalent.  
  
I am now testing kernigh-ppc32-2 (plus a few more changes) on NetBSD; I want to test it on Linux and OpenBSD before I make the pull request.
