# #224 Store entry point arguments in fibre's own stack (s390x) [Merged]

> Username: ltrk2  
> Created at: 2023-05-11 19:26:42 UTC  
> Updated at: 2023-05-12 08:22:26 UTC  
> Merged at: 2023-05-12 04:32:56 UTC  
> Closed at: 2023-05-12 04:32:56 UTC  
> Url: https://github.com/boostorg/context/pull/224  

In the current implementation fibres spawned by other fibres or threads would have the argument of their entry point (`void fiber_entry( transfer_t t)`) be allocated on the stack of their parent, which would then cause segmentation fault if the former outlived the latter.  
  
The x86-64 implementation (`x86_64_sysv_elf` is the combination I tested) supports this use-case, so this change means to achieve feature parity for the s390x implementation in this regard by storing the aforementioned argument in the fibre's own stack.

---

## Comment 1

> Username: olk  
> Created_at: 2023-05-12 04:33:01 UTC  
> Url: https://github.com/boostorg/context/pull/224#issuecomment-1545096412  

ty

---

## Review 2 [Commented]

> Username: Andreas-Krebbel  
> Created_at: 2023-05-12 08:19:31 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/context/pull/224#pullrequestreview-1423944380  

📁 src/asm/make_s390x_sysv_elf_gas.S

```diff
  74 |- 	/* Align the stack to an 8 byte boundary.  */
  74 |+ 	/* Align the stack to an 16 byte boundary.  */
  75 | 	nill    %r2,0xfff0
```

> Username: Andreas-Krebbel  
> Created_at: 2023-05-12 07:37:05 UTC  
> Updated_at: 2023-05-12 08:19:31 UTC  
> Url: https://github.com/boostorg/context/pull/224#discussion_r1192018516  

In fact we only need an 8 byte alignment. So we should rather change the value to 0xfff8.


📁 src/asm/ontop_s390x_sysv_elf_gas.S

```diff
  83 | 
  84 | ontop_fcontext:
  85 |+ 	.machine "z10"
```

> Username: Andreas-Krebbel  
> Created_at: 2023-05-12 07:38:18 UTC  
> Updated_at: 2023-05-12 08:19:31 UTC  
> Url: https://github.com/boostorg/context/pull/224#discussion_r1192019689  

That's not needed here. I only had to add it for make_fcontext to make gas shut up, because it is using mvghi which is a z10 instruction.


📁 src/asm/jump_s390x_sysv_elf_gas.S

```diff
 136 |+ 	   and make sure it isn't deallocated. */
 137 |+ 	la	%r2,ARG_OFFSET(%r15)
 138 |+ 	aghi	%r15,-CONTEXT_SIZE
```

> Username: Andreas-Krebbel  
> Created_at: 2023-05-12 08:08:35 UTC  
> Updated_at: 2023-05-12 08:22:26 UTC  
> Url: https://github.com/boostorg/context/pull/224#discussion_r1192050401  

Good catch! Thanks for your fix!  
  
However, I don't think we can put transfer_t at the bottom of the register save area. The register save area is an ABI defined area of 160 bytes which belongs to the callee. The callee saves its registers in that area in the function prologue and this will usually happen before reading the function arguments. It still works in most of the cases because the bottom slots of the area are mostly unused. But it will break if you build code with -mbackchain where the r0 slot of the register save area is being overwritten. I think we would have to place it ontop of the register save area.   
Also I don't think that CONTEXT_SIZE is the right thing here. It just happens to match the value we need to allocate. What we want here is REG_SAVE_AREA_SIZE+sizeof(transfer_t)  
So perhaps something like:  
```  
	la	%r2,REG_SAVE_AREA_SIZE(%r15)  
	/* REG_SAVE_AREA_SIZE + sizeof(transfer_t) */  
	aghi	%r15,REG_SAVE_AREA_SIZE+16  
```  
  
Passing transfer_t on s390x is a bit more awkward than it is on x86 because we have to pass it via stack while on x86 it is simply passed in a register (or register pair).  
For make_fcontext I agree with you that it is wrong to reuse the transfer_t memory slot in the old context for the argument of the target function. Allocating additional bytes on the stack of the target function looks like the right thing to do here.  
Since a routine called with make_fcontext cannot return without exiting the program we also do not have to care about who will deallocate the additional bytes we have added.


---
