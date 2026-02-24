# #83 Add BOOST_INTERPROCESS_FORCE_NATIVE_EMULATION option [Closed]

> Username: anassinator  
> Created at: 2019-04-04 17:58:07 UTC  
> Updated at: 2021-02-13 23:36:24 UTC  
> Closed at: 2021-02-13 23:36:24 UTC  
> Url: https://github.com/boostorg/interprocess/pull/83  

Addresses #43.  
  
This adds an option `BOOST_INTERPROCESS_NO_FORCE_GENERIC_EMULATION` to disable the forced emulation on Windows, but without the need to manually change the headers or other hacks.  
The performance difference between emulation and non-emulation is 1ms vs 50us using `interprocess_condition` on my machine.  
  
Another option would be to remove the `BOOST_INTERPROCESS_FORCE_GENERIC_EMULATION` define all together, but I think opting into this change would be safer especially since the Windows IPC is apparently experimental and this would break the ABI between versions.

---

## Comment 1

> Username: igaztanaga  
> Created_at: 2021-02-13 23:11:39 UTC  
> Url: https://github.com/boostorg/interprocess/pull/83#issuecomment-778691494  

Thanks for the proposal. To be more consistent with existing macros, I'll rename it as `BOOST_INTERPROCESS_FORCE_NATIVE_EMULATION` and will detect if both `BOOST_INTERPROCESS_FORCE_NATIVE_EMULATION` and `BOOST_INTERPROCESS_FORCE_GENERIC_EMULATION`, giving a compilation error.

---
