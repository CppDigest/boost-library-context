# #233 - Compiling for ARM32/64 on VS2017 broken [Closed]

> Username: DjArt  
> Created at: 2018-09-14 19:20:27 UTC  
> Updated at: 2018-10-09 04:35:49 UTC  
> Closed at: 2018-10-09 04:35:49 UTC  
> Url: https://github.com/boostorg/thread/issues/233  

Due assign in interlocked_read.hpp:  
```  
        inline void* interlocked_read_acquire(void* volatile* x) BOOST_NOEXCEPT  
        {  
            void* const res=  
#if defined(_M_ARM64)  
                __iso_volatile_load64((const volatile __int64*)x);  
#else  
                __iso_volatile_load32((const volatile __int32*)x);  
#endif  
            BOOST_THREAD_DETAIL_COMPILER_BARRIER();  
            __dmb(0xB); // _ARM_BARRIER_ISH, see armintr.h from MSVC 11 and later  
            BOOST_THREAD_DETAIL_COMPILER_BARRIER();  
            return res;  
        }  
```

---

## Comment 1

> Username: viboes  
> Created at: 2018-09-15 04:03:46 UTC  
> Url: https://github.com/boostorg/thread/issues/233#issuecomment-421528756  

Please, could you add the report you are getting?

---

## Comment 2

> Username: DjArt  
> Created at: 2018-09-18 02:44:13 UTC  
> Url: https://github.com/boostorg/thread/issues/233#issuecomment-422235409  

@viboes, sorry for the long wait. Error C2440: int can't be converted to void*.

---

## Comment 3

> Username: viboes  
> Created at: 2018-09-18 21:19:35 UTC  
> Updated at: 2018-09-18 21:20:39 UTC  
> Url: https://github.com/boostorg/thread/issues/233#issuecomment-422559457  

Don't worry.   
  
If the function doesn't returns a `void `, but an `int`, why do we want to store it on a `void *`?

---

## Comment 4

> Username: DjArt  
> Created at: 2018-09-28 10:43:20 UTC  
> Url: https://github.com/boostorg/thread/issues/233#issuecomment-425397181  

`void*` used as platform-unspecified(by size) pointer. So, I think that's better variant for saving original idea of code.

---

## Comment 5

> Username: viboes  
> Created at: 2018-09-30 14:06:17 UTC  
> Url: https://github.com/boostorg/thread/issues/233#issuecomment-425723248  

Oh, I see.   
  
So IIUC, __iso_volatile_load64 returned always a __int64 and __iso_volatile_load32 returned always __int32.   
  
And this size corresponds to the size of a pointer depending on whether _M_ARM64 is defined or not.  
  
How it is that this worked before, Andrei, what do you think?

---

## Comment 6

> Username: DjArt  
> Created at: 2018-09-30 17:07:12 UTC  
> Url: https://github.com/boostorg/thread/issues/233#issuecomment-425735590  

I think, or this code was untested before, or MSVC for ARM has become more strict then before. x86/x86_64 compiler doesn't consider it as mistake.

---

## Comment 7

> Username: viboes  
> Created at: 2018-09-30 20:49:43 UTC  
> Url: https://github.com/boostorg/thread/issues/233#issuecomment-425750962  

I believe the code needs a static assert about the size of both types. Could you add it, please?
