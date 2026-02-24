# #188 Fix: emscripten doesn't support shm [Merged]

> Username: eagleoflqj  
> Created at: 2022-12-24 16:34:20 UTC  
> Updated at: 2023-04-24 11:23:32 UTC  
> Merged at: 2023-04-24 11:00:40 UTC  
> Closed at: 2023-04-24 11:00:41 UTC  
> Url: https://github.com/boostorg/interprocess/pull/188  

I met an error  
```  
wasm-ld: error: build/sysroot/usr/local/lib/librime.a(mapped_file.cc.o): undefined symbol: shmdt  
```  
From https://stackoverflow.com/questions/60285563/shared-memory-sys-shm-h-with-emscripten it seems emscripten doesn't support shm.  
I applied this patch so it linked successfully.

---

## Comment 1

> Username: eagleoflqj  
> Created_at: 2023-04-23 15:11:48 UTC  
> Url: https://github.com/boostorg/interprocess/pull/188#issuecomment-1519089903  

@igaztanaga Could you please take a look at this?

---

## Comment 2

> Username: igaztanaga  
> Created_at: 2023-04-24 11:00:54 UTC  
> Url: https://github.com/boostorg/interprocess/pull/188#issuecomment-1519911642  

Thanks for the patch!

---
