# #204 Fixes for currently broken Darwin ppc/ppc64 assembler [Merged]

> Username: barracuda156  
> Created at: 2022-09-05 21:58:42 UTC  
> Updated at: 2022-10-06 13:35:33 UTC  
> Merged at: 2022-09-18 14:21:19 UTC  
> Closed at: 2022-09-18 14:21:19 UTC  
> Url: https://github.com/boostorg/context/pull/204  

Existing version of Darwin PPC assembler is broken in Boost. This PR makes an initial fix-up.  
  
1. Comment character in Darwin ABI is `;`, not `#`. Corrected everywhere.  
2. Cleanup of typos with register names, which break the build.  
3. Removed unsupported directive `@plt`, which breaks the build.  
4. Unlike ELF, Darwin does not use TOC, including on `ppc64`.  
  
I do not really get why FPR env has been removed at some point for `ppc64`, while brought back to `ppc32`. Is it just another episode of careless borrowing across ABIs? Here, for example, `libcontext` has FPR support in `ppc64`:  
https://github.com/twlostow/libcontext/blob/master/jump_ppc64_sysv_macho_gas.S  
https://github.com/twlostow/libcontext/blob/master/make_ppc64_sysv_macho_gas.S  
  
P. S. @iains If you have a few minutes to take a look, that would be great. We really need functioning context/coroutine libs in Boost on PPC.  
This version builds and apparently works: https://github.com/twlostow/libcontext  
However it lacks `ontop_ppc*` components, and may not match very well with the current Boost ABI.

---

## Comment 1

> Username: olk  
> Created_at: 2022-09-18 14:21:23 UTC  
> Url: https://github.com/boostorg/context/pull/204#issuecomment-1250319078  

ty

---

## Comment 2

> Username: olk  
> Created_at: 2022-10-04 05:25:33 UTC  
> Url: https://github.com/boostorg/context/pull/204#issuecomment-1266413726  

maybe fixed by https://github.com/boostorg/context/pull/204#issue-1362499215

---

## Comment 3

> Username: barracuda156  
> Created_at: 2022-10-04 17:04:45 UTC  
> Url: https://github.com/boostorg/context/pull/204#issuecomment-1267302144  

> maybe fixed by [#204 (comment)](https://github.com/boostorg/context/pull/204#issue-1362499215)  
  
@olk The error reported in that ticket occurs on M1 and seems to be build system-related (judging by the quote at least). Of course, ppc64 should not be built on 10.6+, since no version of 10.6 has ppc64 slices, and 10.7+ has no ppc at all.  
I am not able to test anything for M1, no hardware.  
  
We may need some minor extra fixes for PPC, I will try to find time for those this week. Just back to my PowerMacs, so finally can test stuff natively and not just in Rosetta.  
(What was done in this PR is required but may not be sufficient. Gonna confirm that soon and possibly open a new PR to finalize things – after testing on 10.5.8, 10A190 and 10.6.8 Rosetta.)

---

## Comment 4

> Username: barracuda156  
> Created_at: 2022-10-06 13:35:33 UTC  
> Url: https://github.com/boostorg/context/pull/204#issuecomment-1270061890  

@olk Looks like this is the fix that is still required: https://github.com/barracuda156/context/commit/a3b0224b0c6dcf92a7bf06bff47bc53ccbc7b74f  
  
It mirrors existing code in `make_ppc64_sysv_macho_gas.S` and is identical to `ppc32` code here: https://github.com/barracuda156/libcontext/blob/65d2d66b557eecd8f46c4e6e0529eece6955bb5c/make_ppc32_sysv_macho_gas.S#L95 – that version of `context` is older and stripped-down, but fully functional: https://github.com/twlostow/libcontext/pull/1#issuecomment-1210586311  
  
I want to verify that everything works correctly, and after that will submit a PR.

---
