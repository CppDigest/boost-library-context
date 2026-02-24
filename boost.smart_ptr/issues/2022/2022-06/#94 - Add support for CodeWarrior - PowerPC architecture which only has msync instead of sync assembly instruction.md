# #94 - Add support for CodeWarrior / PowerPC architecture which only has msync instead of sync assembly instruction [Closed]

> Username: jwillikers  
> Created at: 2022-06-21 17:53:56 UTC  
> Updated at: 2022-06-22 15:41:01 UTC  
> Closed at: 2022-06-22 15:41:01 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/94  

The [atomic_decrement](https://github.com/boostorg/smart_ptr/blob/f2ab3b21f0e9de92cbf5f27115e1e5ddf50f0e13/include/boost/smart_ptr/detail/sp_counted_base_cw_ppc.hpp#L61) function in [smart_ptr/detail/sp_counted_base_cw_ppc.hpp](include/boost/smart_ptr/detail/sp_counted_base_cw_ppc.hpp) uses the `sync` assembly instruction.  
On some PowerPC platforms, such as the [e200z4RM](https://www.nxp.com/files-static/32bit/doc/ref_manual/e200z4RM.pdf), only the `msync` assembly instruction is available instead of the `sync` function.  
In order to use Boost.SmartPtr on these platforms, would it be possible to add a configuration option to allow using the `msync` instruction instead of the `sync` instruction when applicable?

---

## Comment 1

> Username: pdimov  
> Created at: 2022-06-21 18:25:26 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/94#issuecomment-1162156787  

I consider this a request for unmarking this implementation as obsolete. :-)  
  
https://github.com/boostorg/smart_ptr/blob/f2ab3b21f0e9de92cbf5f27115e1e5ddf50f0e13/include/boost/smart_ptr/detail/sp_counted_base_cw_ppc.hpp#L38  
  
What does the `__sync();` intrinsic do on the platforms that lack the `sync` instruction? How about `__lwsync();`, is that supported? (`sync` is actually overkill there, `lwsync` is a better option if available.)

---

## Comment 2

> Username: jwillikers  
> Created at: 2022-06-21 21:49:55 UTC  
> Updated at: 2022-06-21 22:45:05 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/94#issuecomment-1162401057  

@pdimov Yes, I guess this means the implementation may not be obsolete quite yet.  
  
Looking into it a bit, it appears that if `__PPCZen__`, `__PPCe500__`, or `__PPCe500v2__` are _not_ defined then the `sync` assembly instruction is available. Otherwise, the primitive is either `se_isync` if `VLE_ON` is true or `msync` if it is not defined. That's about all I can tell looking at a built-in function call in CodeWarrior to flush the cache. I don't see anything about `lwsync` anywhere. I'll probably be able to dig in a bit more in the coming days.

---

## Comment 3

> Username: jwillikers  
> Created at: 2022-06-21 22:51:26 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/94#issuecomment-1162439131  

Apparently this issue has been worked around for years be simply replacing the single use of `sync` with `msync` at my company. I've created PR #95  with what I think the changes could look like. I assumed that the `VLE_ON` macro should be considered and the `se_isync` instruction used in that case, though I'm not exactly sure about that. The manual I linked above should contain all the necessary information on the assembly instructions.

---

## Comment 4

> Username: pdimov  
> Created at: 2022-06-21 23:41:54 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/94#issuecomment-1162465869  

I was wondering whether we could replace the `sync` instruction in the assembly with a CodeWarrior intrinsic function outside of the asm block, one of `__sync();` or `__lwsync();` if they happen to do the right thing on these platforms. (I see in https://www.nxp.com/docs/en/reference-manual/PA_Build_RM.pdf page 317 that `__sync()` at least should be supported by CodeWarrior for PPC. `__lwsync()` is supported by the IBM compilers, maybe CodeWarrior doesn't have it.)

---

## Comment 5

> Username: pdimov  
> Created at: 2022-06-21 23:46:07 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/94#issuecomment-1162467974  

I'm not sure about the `VLE_ON` macro because (according to the same document page 375) this looks like something you could turn on and off per function.  
  
If we aren't sure it works maybe we should just use `msync` there for now.

---

## Comment 6

> Username: jwillikers  
> Created at: 2022-06-22 01:37:36 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/94#issuecomment-1162526636  

> I was wondering whether we could replace the `sync` instruction in the assembly with a CodeWarrior intrinsic function outside of the asm block, one of `__sync();` or `__lwsync();` if they happen to do the right thing on these platforms. (I see in https://www.nxp.com/docs/en/reference-manual/PA_Build_RM.pdf page 317 that `__sync()` at least should be supported by CodeWarrior for PPC. `__lwsync()` is supported by the IBM compilers, maybe CodeWarrior doesn't have it.)  
  
Yes, I did see mention of `__sync();`. I'll see if that works on our end and use that if it does.

---

## Comment 7

> Username: jwillikers  
> Created at: 2022-06-22 01:38:03 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/94#issuecomment-1162526868  

> I'm not sure about the `VLE_ON` macro because (according to the same document page 375) this looks like something you could turn on and off per function.  
>   
> If we aren't sure it works maybe we should just use `msync` there for now.  
  
Agreed. I'll nix that.

---

## Comment 8

> Username: jwillikers  
> Created at: 2022-06-22 02:10:09 UTC  
> Updated at: 2022-06-22 02:26:39 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/94#issuecomment-1162543638  

> > I was wondering whether we could replace the `sync` instruction in the assembly with a CodeWarrior intrinsic function outside of the asm block, one of `__sync();` or `__lwsync();` if they happen to do the right thing on these platforms. (I see in https://www.nxp.com/docs/en/reference-manual/PA_Build_RM.pdf page 317 that `__sync()` at least should be supported by CodeWarrior for PPC. `__lwsync()` is supported by the IBM compilers, maybe CodeWarrior doesn't have it.)  
>   
> Yes, I did see mention of `__sync();`. I'll see if that works on our end and use that if it does.  
  
`__sync();` works! With the caveat of an annoying warning flooding the console output:  
  
```  
warning: sync() has changed to msync() read book E to understand the differences (while sync() still works msync() does not have this message)  
```  
  
I've updated my MR to use the intrinsic.

---

## Comment 9

> Username: pdimov  
> Created at: 2022-06-22 03:06:36 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/94#issuecomment-1162584270  

The warning implies they don't want us to use `__sync` :-)  
  
I did read book E, as instructed, and it says:  
  
```  
Programming Note  
msync replaces the PowerPC sync instruction. msync uses the same opcode as sync  
such that PowerPC applications calling for a sync instruction will get the Book E msync  
when executed on an Book E implementation. The functionality of msync is identical to  
sync except that msync also does not complete until all previous storage accesses  
complete. mbar is provided in the Book E for those occasions when only ordering of  
storage accesses is required without execution synchronization.  
See also Appendix D on page 397.  
```  
  
I looked at Appendix D to see whether `mbar` will suffice here, but it looks like `mbar` doesn't prevent loads from sinking below the store, so `msync` would still be required.  
  
So I don't insist on using `__sync` here if this will cause tons of warnings; you can go back to the warning-free alternative of `ifdef` and `msync` if you prefer. (The end result in terms of instructions emitted should be identical.)

---

## Comment 10

> Username: jwillikers  
> Created at: 2022-06-22 11:30:38 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/94#issuecomment-1162982678  

Since there's no obvious option to disable the warning, better to play it safe and not make a bunch of build logs unreadable. I've reverted my `__sync` commit. Everything looks good-to-go.
