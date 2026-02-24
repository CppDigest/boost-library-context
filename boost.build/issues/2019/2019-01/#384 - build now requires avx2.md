# #384 - build now requires avx2 [Closed]

> Username: btiplitz  
> Created at: 2019-01-07 21:13:15 UTC  
> Updated at: 2019-01-07 21:21:54 UTC  
> Closed at: 2019-01-07 21:21:54 UTC  
> Url: https://github.com/boostorg/build/issues/384  

The compiler detection when upgrading from 1.65 to 1.68, now sets the flag compiler-supports-avx2 which is then used to build in avx2 instructions into the library within the log library.  There appears to be no way to disable this other than modifying the log library (or changing the jam file after the failure).    
  
I only noticed this as I upgraded to gcc-4.9.4 with C++11 support but the binutils does not support avx2 instructions, so I can't build boost 1.68 on the target host as delivered.

---

## Comment 1

> Username: swatanabe  
> Created at: 2019-01-07 21:19:29 UTC  
> Url: https://github.com/boostorg/build/issues/384#issuecomment-452085539  

AMDG  
  
This should probably be filed against Boost.Log.  
  
In Christ,  
Steven Watanabe

---

## Comment 2

> Username: grafikrobot  
> Created at: 2019-01-07 21:21:54 UTC  
> Url: https://github.com/boostorg/build/issues/384#issuecomment-452086205  

It's definitely a problem with Boost.Log. Please report it there https://github.com/boostorg/log/issues
