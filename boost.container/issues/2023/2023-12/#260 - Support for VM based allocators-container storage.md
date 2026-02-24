# #260 - Support for VM based allocators/container storage [Closed]

> Username: psiha  
> Created at: 2023-12-18 18:46:41 UTC  
> Updated at: 2024-09-30 21:35:47 UTC  
> Closed at: 2024-09-30 21:35:46 UTC  
> Url: https://github.com/boostorg/container/issues/260  

I have a VM/mmap abstracting library (https://github.com/psiha/vm) and recently I tried to leverage it in conjuction with Boost.Container to create persisting STL-like containers (at least ones with contiguous storage).  
  
First I tried hacking up an '[extended allocator](https://github.com/psiha/vm/blob/master/include/psi/vm/allocator.hpp)' (that provides the extend and shrink functionality, which maps great onto the VM 'mindset') - [however this failed because Boost.Container has the assumption that an extended allocation remains at the same base address](https://github.com/psiha/vm/blob/949bb872802650fae602451ada428f60bc5c1762/include/psi/vm/allocator.hpp#L117C23-L117C23) - which need not hold with VM (re)allocations - if you run out if _address space_ the allocation can be expanded 'in-place' (i.e. without any copying) yet still change its (virtual) address (and this is perfectly safe for trivially moveable types) - would you consider changing your logic to support such scenarios (i.e. checking/calculating a possible base address offset first after a successful expand, bwd or fwd, operation)?  
  
Secondly I tried [rolling my own VM-based vector](https://github.com/psiha/vm/blob/c0a930a4d989d25dd1aa639fab83b28a9507c0f3/include/psi/vm/vector.hpp#L240) to then use it as a backing sequence for boost::container::flat_set. This initially failed because I (re)used checked/'safe' iterators in debug builds as provided by default by Microsoft's STL yet Boost.Container in various places tries to convert an end iterator into its underlying 'raw' pointer or reference which causes assertion failures at runtime with checked iterators (as it is not actually allowed). I was able to workaround this by using non-checked iterators when interoping with Boost.Container yet this is something that should be fixed.  
(I understand this is actually a separate issue/bug so I'll file it also as such).

---

## Comment 1

> Username: igaztanaga  
> Created at: 2024-09-30 21:35:46 UTC  
> Url: https://github.com/boostorg/container/issues/260#issuecomment-2384185109  

I've reviewed a bit your VM library and I'm afraid VM/mmap abstractions require a different design, I'm afraid Boost.Container is not appropriate for this purpose. On the other hand, thanks to your reports, some Boost.Container bugs have been fixed, so many thanks for all.
