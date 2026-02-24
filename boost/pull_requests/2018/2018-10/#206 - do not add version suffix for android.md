# #206 do not add version suffix for android [Merged]

> Username: huangqinjin  
> Created at: 2018-10-12 02:28:08 UTC  
> Updated at: 2023-04-04 00:46:49 UTC  
> Merged at: 2023-04-03 21:50:20 UTC  
> Closed at: 2023-04-03 21:50:20 UTC  
> Url: https://github.com/boostorg/boost/pull/206  

Android studio could automatically pack shared libraries into final apk. But if we link to `libboost_system.so`, which is a soft link to `libboost_system.so.1.68.0`, only the former will be packed.

---

## Comment 1

> Username: mclow  
> Created_at: 2018-10-12 02:34:14 UTC  
> Url: https://github.com/boostorg/boost/pull/206#issuecomment-429184164  

If you add a comment why android is in the list, then this will be fine. See line #197/8, etc of the file you're changing.

---

## Comment 2

> Username: huangqinjin  
> Created_at: 2018-10-12 02:52:26 UTC  
> Url: https://github.com/boostorg/boost/pull/206#issuecomment-429186988  

@mclow Should I add the comment in file I changed?

---

## Comment 3

> Username: mclow  
> Created_at: 2018-10-12 03:05:10 UTC  
> Url: https://github.com/boostorg/boost/pull/206#issuecomment-429189168  

> @mclow Should I add the comment in file I changed?  
  
Yes, please.

---

## Comment 4

> Username: Bjoe  
> Created_at: 2019-07-05 16:42:55 UTC  
> Url: https://github.com/boostorg/boost/pull/206#issuecomment-508812710  

Hm, I updated to the newest boost version 1.70 and unfortunately I get the same error if I pack shared boost libs on android as in 1.67 .... I saw this PR last year and I expected that this fix to be included in 1.70 .... but it is not :-(  ... is there something missing where I can help?

---

## Comment 5

> Username: pengxu-github  
> Created_at: 2020-04-03 09:07:09 UTC  
> Url: https://github.com/boostorg/boost/pull/206#issuecomment-608322940  

> @mclow Should I add the comment in file I changed?  
  
sorry to trobule you，i picked your commit "do not add version suffix for android" and compile boost.filesystem to libboost_filesystem.so, but when i use libboost_filesystem.so in my apk, it crashed for "dlopen failed: library "libboost_filesystem.so.1.72.0" not found".   
may you kindly save me for this crash? thanks very much

---

## Comment 6

> Username: huangqinjin  
> Created_at: 2020-04-03 13:05:24 UTC  
> Url: https://github.com/boostorg/boost/pull/206#issuecomment-608421987  

@pengxu-github check if you build correctly. The SONAME should not end with version info. Then delete cmake cache and rebuild your apk.  
  
```  
arm64-v8a$ readelf -d libboost_filesystem.so   
  
Dynamic section at offset 0x18a58 contains 28 entries:  
  Tag        Type                         Name/Value  
 0x0000000000000001 (NEEDED)             Shared library: [libc++_shared.so]  
 0x0000000000000001 (NEEDED)             Shared library: [libm.so]  
 0x0000000000000001 (NEEDED)             Shared library: [libdl.so]  
 0x0000000000000001 (NEEDED)             Shared library: [libc.so]  
 0x000000000000000e (SONAME)             Library soname: [libboost_filesystem.so]  
 0x0000000000000019 (INIT_ARRAY)         0x197e8  
 0x000000000000001b (INIT_ARRAYSZ)       8 (bytes)  
 0x000000000000001a (FINI_ARRAY)         0x197f0  
 0x000000000000001c (FINI_ARRAYSZ)       16 (bytes)  
  
```

---

## Comment 7

> Username: pengxu-github  
> Created_at: 2020-04-14 09:05:11 UTC  
> Url: https://github.com/boostorg/boost/pull/206#issuecomment-613319137  

> @pengxu-github check if you build correctly. The SONAME should not end with version info. Then delete cmake cache and rebuild your apk.  
>   
> ```  
> arm64-v8a$ readelf -d libboost_filesystem.so   
>   
> Dynamic section at offset 0x18a58 contains 28 entries:  
>   Tag        Type                         Name/Value  
>  0x0000000000000001 (NEEDED)             Shared library: [libc++_shared.so]  
>  0x0000000000000001 (NEEDED)             Shared library: [libm.so]  
>  0x0000000000000001 (NEEDED)             Shared library: [libdl.so]  
>  0x0000000000000001 (NEEDED)             Shared library: [libc.so]  
>  0x000000000000000e (SONAME)             Library soname: [libboost_filesystem.so]  
>  0x0000000000000019 (INIT_ARRAY)         0x197e8  
>  0x000000000000001b (INIT_ARRAYSZ)       8 (bytes)  
>  0x000000000000001a (FINI_ARRAY)         0x197f0  
>  0x000000000000001c (FINI_ARRAYSZ)       16 (bytes)  
> ```  
  
thanks for your help, i found the error, i build the so with linux gcc not android.  
finally i used the project Boost-for-Android in github.

---

## Comment 8

> Username: Bjoe  
> Created_at: 2020-12-05 15:01:09 UTC  
> Url: https://github.com/boostorg/boost/pull/206#issuecomment-739268811  

@mclow Hi. Last year (2019) I was in the "boost meeting" on the Meeting C++ and pointing to this PR and if we can merge this. I was recommended to contact you but unfortunately I missed you. This year Meeting C++ was only online ... so I try again on this PR, because I think this issue/fix is somehow forgotten or is there anything that we missed here? Or should I wrote a mail to the Boost mailing list? not sure....  
  
It doesn't have to be immediately and it is clear that this fix will not come in 1.75.0 ... but maybe 1.76? At least 1.77 would be nice if it was doable? Thanks for your help.

---

## Comment 9

> Username: kambala-decapitator  
> Created_at: 2023-02-18 11:14:48 UTC  
> Url: https://github.com/boostorg/boost/pull/206#issuecomment-1435645680  

merging this PR is highly desired as otherwise shared build of Boost is completely unusable on Android out of the box (one should manually apply hack to adjust SONAME in the built `.so` to be versionless)

---
