# #444 - Stack overflow in b2 [Open]

> Username: pdimov  
> Created at: 2019-05-27 13:47:52 UTC  
> Updated at: 2021-06-11 01:55:45 UTC  
> Url: https://github.com/boostorg/build/issues/444  

As mentioned in https://github.com/boostorg/bcp/pull/5, I'm getting a stack overflow (due to apparently endless recursion) in b2. Reproducing it is a bit convoluted at this point:  
  
1. You need to switch `tools/bcp` to the `feature/update-specials` branch  
2. Build bcp with `b2 tools/bcp` from the root  
3. Issue `dist/bin/bcp program_options build /some/dir`  
4. `cd /some/dir`  
5. `./bootstrap`  
6. `./b2 libs/program_options/test`

---

## Comment 1

> Username: stale[bot]  
> Created at: 2021-06-11 01:55:07 UTC  
> Url: https://github.com/boostorg/build/issues/444#issuecomment-859203381  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2  
This issue has been automatically marked as "transition" to indicate the potential for needing transition to the new B2 development project.
