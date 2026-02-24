# #153 - Unguarded unconditional use of constexpr [Closed]

> Username: Flamefire  
> Created at: 2022-03-09 15:15:01 UTC  
> Updated at: 2022-03-09 20:03:12 UTC  
> Closed at: 2022-03-09 16:34:54 UTC  
> Url: https://github.com/boostorg/wave/issues/153  

As noted on the ML Wave does no longer compile on VS 2013 due to missing `constexpr` support.  
  
This is required since https://github.com/boostorg/wave/commit/1db25211a4ba1ebec30f2bec0e1cd04c8bd30174 by @jefftrull  
  
Solution: Add `cxx11_constexpr` to the requirements at https://github.com/boostorg/wave/blob/develop/build/Jamfile.v2#L15 or use `BOOST_CONSTEXPR` on those functions

---

## Comment 1

> Username: jefftrull  
> Created at: 2022-03-09 20:03:12 UTC  
> Url: https://github.com/boostorg/wave/issues/153#issuecomment-1063315546  

Thanks for your help!
