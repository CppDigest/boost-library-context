# #99 - Possible resource leak in ucontext implementation [Closed]

> Username: prograholic  
> Created at: 2019-02-21 03:25:56 UTC  
> Updated at: 2019-02-22 08:29:31 UTC  
> Closed at: 2019-02-22 08:29:22 UTC  
> Url: https://github.com/boostorg/context/issues/99  

Hi,   
  
I see some strange pattern in continuation_ucontext.hpp:  
  
**Line 295**: we construct new object using placement new operator https://github.com/boostorg/context/blob/develop/include/boost/context/continuation_ucontext.hpp#L295  
**Line 302**: we throw exception https://github.com/boostorg/context/blob/develop/include/boost/context/continuation_ucontext.hpp#L302  
  
Same pattern we can find in function `create_context2`  
  
Do we need to destroy our object before throwing exception?

---

## Comment 1

> Username: olk  
> Created at: 2019-02-22 07:11:37 UTC  
> Updated at: 2019-02-22 07:11:55 UTC  
> Url: https://github.com/boostorg/context/issues/99#issuecomment-466297186  

you are right - before `throw` a `salloc.allocate(sctx)` is missing  
ty

---

## Comment 2

> Username: viccpp  
> Created at: 2019-02-22 07:35:37 UTC  
> Url: https://github.com/boostorg/context/issues/99#issuecomment-466302968  

```c++  
record->~capture_t();  
```  
?

---

## Comment 3

> Username: olk  
> Created at: 2019-02-22 08:06:26 UTC  
> Url: https://github.com/boostorg/context/issues/99#issuecomment-466309962  

capture_record/fiber_capture_record do not have member variables or execute special code in the dtor

---

## Comment 4

> Username: viccpp  
> Created at: 2019-02-22 08:12:13 UTC  
> Url: https://github.com/boostorg/context/issues/99#issuecomment-466311305  

Yes. But destructor is not trivial (and virtual)! I know that it 'just works' but still. Wouldn't it be better to be C++-conformant here?

---

## Comment 5

> Username: olk  
> Created at: 2019-02-22 08:29:22 UTC  
> Updated at: 2019-02-22 08:29:31 UTC  
> Url: https://github.com/boostorg/context/issues/99#issuecomment-466315765  

probably yes - fixed  
ty
