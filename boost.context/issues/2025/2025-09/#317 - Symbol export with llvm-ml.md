# #317 - Symbol export with llvm-ml [Open]

> Username: boberfly  
> Created at: 2025-09-10 07:06:54 UTC  
> Updated at: 2025-09-10 07:06:54 UTC  
> Url: https://github.com/boostorg/context/issues/317  

Hi all,  
  
I thought I'd share 2 patches that I needed to successfully compile boost.context on clang-cl with llvm-ml. I think these can't exactly be applied as-is without breaking existing functionality, but it basically stems down to `/quiet` not being supported, and needing to pass `--m64` to enable 64-bit aka the `mt64.exe` equivalent. The other thing is how symbols get exported, the `PROC` call doesn't support `EXPORT` however `__declspec(dllexport)` on the C signature does work.  
  
[cmake.patch](https://github.com/user-attachments/files/22249116/cmake.patch)  
  
[fcontext.patch](https://github.com/user-attachments/files/22249117/fcontext.patch)  
  
Kind regards
