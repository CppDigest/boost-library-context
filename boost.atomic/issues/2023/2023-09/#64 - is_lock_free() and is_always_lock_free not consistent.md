# #64 - is_lock_free() and is_always_lock_free not consistent. [Closed]

> Username: Sernior  
> Created at: 2023-09-17 19:30:10 UTC  
> Updated at: 2023-09-17 20:21:45 UTC  
> Closed at: 2023-09-17 19:31:25 UTC  
> Url: https://github.com/boostorg/atomic/issues/64  

![screen_2023-09-17_21-16-31](https://github.com/boostorg/atomic/assets/56980041/edd74618-37c5-4d57-aaa9-1fb7ae8704da)  
  
bits128 as the name imply is a 128 bit sized struct:  
  
`  
struct bits128{  
    uint64_t a;  
    uint64_t b;  
};  
`

---

## Comment 1

> Username: Sernior  
> Created at: 2023-09-17 20:21:45 UTC  
> Url: https://github.com/boostorg/atomic/issues/64#issuecomment-1722560132  

My bad, was not linking mcx16 which is required on GCC to being able to use the cmpxchg16b.  
If you like me are having the same problem that`s most likely what you need.
