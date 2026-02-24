# #1149 Add randomize_va_space to drone [Merged]

> Username: mborland  
> Created at: 2024-07-02 20:55:16 UTC  
> Updated at: 2024-07-03 17:09:18 UTC  
> Merged at: 2024-07-03 17:09:14 UTC  
> Closed at: 2024-07-03 17:09:14 UTC  
> Url: https://github.com/boostorg/math/pull/1149  



---

## Comment 1

> Username: mborland  
> Created_at: 2024-07-03 13:27:35 UTC  
> Url: https://github.com/boostorg/math/pull/1149#issuecomment-2206078667  

@sdarwin both `sudo sysctl vm.mmap_rnd_bits=28` and 30 give the same memory mapping error. Any other ideas, or does it need to go at an earlier point in the script?

---

## Comment 2

> Username: sdarwin  
> Created_at: 2024-07-03 14:22:28 UTC  
> Url: https://github.com/boostorg/math/pull/1149#issuecomment-2206268207  

It should be 28: `sudo sysctl vm.mmap_rnd_bits=28` , and inside the` if [[ $(uname) == "Linux" ]]; then` , however if that is not working, try to use clang 18.1   
  
https://github.com/google/sanitizers/issues/1716   
  
Newer versions of TSan (LLVM 18.1.0 onwards: https://github.com/llvm/llvm-project/commit/0784b1eefa36d4acbb0dacd2d18796e26313b6c5) will automatically re-exec without ASLR, if the layout is incompatible. Additionally, TSan in LLVM 18.1.0 onwards can support 30 bits of ASLR entropy without disabling ASLR (https://github.com/llvm/llvm-project/commit/7d039effc4930be9240446a4241d268a39960e0b).

---

## Comment 3

> Username: mborland  
> Created_at: 2024-07-03 14:56:26 UTC  
> Url: https://github.com/boostorg/math/pull/1149#issuecomment-2206418691  

Clang-18 seems to be working fine. Thanks for the help!

---
