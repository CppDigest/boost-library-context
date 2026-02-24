# #35 Fixed options for cross-compilation. [Merged]

> Username: NiklasAngare  
> Created at: 2017-02-12 12:29:08 UTC  
> Updated at: 2017-02-12 20:57:55 UTC  
> Merged at: 2017-02-12 20:57:32 UTC  
> Closed at: 2017-02-12 20:57:32 UTC  
> Url: https://github.com/boostorg/interprocess/pull/35  

Replaced \<host-os\> with \<target-os\> so that the correct options for the target are selected when cross-compiling. When not cross-compiling, it makes no difference as target-os by default mirrors host-os.  
  
Without this, I get the linker error "cannot find -lrt" on my regression test runner for QNX 6.6.0 which is cross-compiling from Linux to QNX.

---

## Comment 1

> Username: igaztanaga  
> Created_at: 2017-02-12 20:57:55 UTC  
> Url: https://github.com/boostorg/interprocess/pull/35#issuecomment-279248251  

Thanks for the patch.

---
