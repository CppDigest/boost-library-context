# #137 - x86 optimized operators are slower than generic version [Closed]

> Username: baryluk  
> Created at: 2022-10-13 12:15:51 UTC  
> Updated at: 2023-04-15 18:24:54 UTC  
> Closed at: 2023-04-15 18:24:54 UTC  
> Url: https://github.com/boostorg/uuid/issues/137  

https://github.com/boostorg/uuid/blob/develop/include/boost/uuid/detail/uuid_x86.ipp  
  
All these custom 128-bit loads are unnecessary in x86 version of operators.  
  
Just use memcmp like in generic version.  
  
```  
generic_compare(uuid const&, uuid const&):          # @generic_compare(uuid const&, uuid const&)  
        vmovdqu xmm0, xmmword ptr [rdi]  
        vpxor   xmm0, xmm0, xmmword ptr [rsi]  
        vptest  xmm0, xmm0  
        sete    al  
        ret  
x86_compare(uuid const&, uuid const&):              # @x86_compare(uuid const&, uuid const&)  
        vlddqu  xmm0, xmmword ptr [rdi]  
        vlddqu  xmm1, xmmword ptr [rsi]  
        vxorpd  xmm0, xmm1, xmm0  
        vptest  xmm0, xmm0  
        sete    al  
        ret  
```  
  
  
gcc trunk and clang.  
  
https://godbolt.org/z/b3jnaK1v7  
  
  
gcc 12.2, produces suboptimal code for memcmp, creating 64-bit loads. But newer gcc works fine.  
  
In the future it should be fine to just remove x86 optimized version, and use `memcmp`. But for now, combing a load and xor would be the best.

---

## Comment 1

> Username: jeking3  
> Created at: 2022-10-25 21:42:24 UTC  
> Url: https://github.com/boostorg/uuid/issues/137#issuecomment-1291175022  

@Lastique thoughts?

---

## Comment 2

> Username: Lastique  
> Created at: 2022-10-26 06:10:00 UTC  
> Updated at: 2022-10-26 06:15:52 UTC  
> Url: https://github.com/boostorg/uuid/issues/137#issuecomment-1291549248  

I strongly prefer to not rely on the compiler doing the right thing because this is unreliable and often depends on the surrounding code and compiler version. The OP admits this himself.  
  
I expect the two instruction sequences above to have close to equal performance on most modern CPUs, although for CPUs supporting AVX I would probably replace `vlddqu` with `vmovdqu` as the former has one cycle extra latency on Skylake and later and cannot be merged into the following `vpxor`/`vxorpd`, resulting in slightly larger code. `lddqu` is preferable on older Intel CPUs of Netburst era (Prescott) and indifferent for AMD CPUs. Until AVX it also cannot offer code size reduction as memory operands must be aligned for legacy SSE instructions.  
  
Bottom line, the code can be better optimized for AVX (I can submit a PR), but otherwise I would strongly prefer to keep the hand optimized version. Anyone who has a problem with the optimized versions can already disable them by defining `BOOST_UUID_NO_SIMD`.
