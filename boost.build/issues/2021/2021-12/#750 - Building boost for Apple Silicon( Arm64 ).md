# #750 - Building boost for Apple Silicon( Arm64 ) [Closed]

> Username: clusty  
> Created at: 2021-12-15 21:30:07 UTC  
> Updated at: 2021-12-15 21:59:15 UTC  
> Closed at: 2021-12-15 21:59:15 UTC  
> Url: https://github.com/boostorg/build/issues/750  

I am trying to build Boost 1.78 from source code, but failing:  
I am trying to use clang 13.0 .  
I am currently running:  
./bootstrap.sh  
./b2  cxxflags="-arch arm64" -a  
  
After a bit of logs I get:  
  
> error: No best alternative for libs/context/build/asm_sources  
>     next alternative: required properties: <abi>aapcs <address-model>32 <architecture>arm <binary-format>elf <threading>multi <toolset>clang  
>         not matched  
>     next alternative: required properties: <abi>aapcs <address-model>32 <architecture>arm <binary-format>elf <threading>multi <toolset>gcc  
>         not matched  
>     next alternative: required properties: <abi>aapcs <address-model>32 <architecture>arm <binary-format>elf <threading>multi <toolset>qcc  
> ...  
  
Is there something I am doing wrong. Known limitation ?

---

## Comment 1

> Username: mclow  
> Created at: 2021-12-15 21:58:20 UTC  
> Url: https://github.com/boostorg/build/issues/750#issuecomment-995245778  

You will probably get more help asking this question on the boost-users mailing list: boost-users@lists.boost.org  
  
What happens if you just build as: `./b2`?

---

## Comment 2

> Username: github-actions[bot]  
> Created at: 2021-12-15 21:59:14 UTC  
> Url: https://github.com/boostorg/build/issues/750#issuecomment-995246328  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2 Please consider following up at https://github.com/bfgroup/b2/issues
