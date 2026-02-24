# #70 - boost.lockfree tagged_ptr conflict with Hardware-assisted AddressSanitizer(HWASan) from LLVM project [Open]

> Username: lbtrace  
> Created at: 2021-07-13 03:25:30 UTC  
> Updated at: 2021-07-16 07:27:35 UTC  
> Url: https://github.com/boostorg/lockfree/issues/70  

As we know, boost using tagged_ptr avoid CAS ABA issue. But in aarch64, tagged_ptr_ptrcompression using top bits of pointer, but HWASan also using top bits of pointer, so conflict.  
HWASan help detect memory bugs in LLVM project, so should boost lockfree compat HWASan ?

---

## Comment 1

> Username: timblechmann  
> Created at: 2021-07-13 03:51:13 UTC  
> Url: https://github.com/boostorg/lockfree/issues/70#issuecomment-878756014  

do aarch64 have any form of double-width compare and swap? disabling the ptrcompression would probably result in using 128bit atomics, which would have to be emulated by using locks (with all the side effects that one could not use the data structures in shared memory for example).  
  
would it be possible to simply exclude the ptrcompression code from hwasan via compiler attributes?

---

## Comment 2

> Username: lbtrace  
> Created at: 2021-07-14 03:28:31 UTC  
> Url: https://github.com/boostorg/lockfree/issues/70#issuecomment-879557109  

@timblechmann thanks your reply.   
- Actually, in Aarch64 from armv8.1 "CASP" instruction can support 128 bits CAS operation.   
For detail you can check https://en.wikichip.org/wiki/arm/armv8.1 and https://developer.arm.com/documentation/100076/0100/a64-instruction-set-reference/a64-data-transfer-instructions/caspa--caspal--casp--caspl--caspal--casp--caspl  
  
- Enable hwasan usually via compiler flags "-fsanitize=hwaddress", we can exclude ptrcompression, but if we run boost in Hwasan device(only libc enable hwasan, boost not enable hwasan compile), like Android, we can't avoid this issue(because boost using memory from libc, boost ptrcompression still conflict with libc memory pointer enabled hwasan)

---

## Comment 3

> Username: timblechmann  
> Created at: 2021-07-14 03:39:39 UTC  
> Url: https://github.com/boostorg/lockfree/issues/70#issuecomment-879560787  

> Actually, in Aarch64 from armv8.1 "CASP" instruction can support 128 bits CAS operation  
  
interesting. i don't have access to any armv8.1 (just a rpi4, which seems to be armv8.0). could you try to change https://github.com/boostorg/lockfree/blob/develop/include/boost/lockfree/detail/prefix.hpp#L26 to disable pointer compression for arm 8.1.0 and check it the atomics are lockfree? also i wonder if there are any performance implications that may favour pointer compression with 64bit cas over 128 cas ...

---

## Comment 4

> Username: lbtrace  
> Created at: 2021-07-16 07:27:35 UTC  
> Url: https://github.com/boostorg/lockfree/issues/70#issuecomment-881237629  

@timblechmann I can't identify minor version "x" of armv8.x, maybe I only disable pointer compression for hwasan test
