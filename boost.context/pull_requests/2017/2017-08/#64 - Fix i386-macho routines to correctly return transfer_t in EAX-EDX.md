# #64 Fix i386/macho routines to correctly return transfer_t in EAX/EDX [Merged]

> Username: mjohns  
> Created at: 2017-08-02 19:50:14 UTC  
> Updated at: 2017-08-03 05:20:40 UTC  
> Merged at: 2017-08-03 05:20:30 UTC  
> Closed at: 2017-08-03 05:20:30 UTC  
> Url: https://github.com/boostorg/context/pull/64  

The OSX 32bit ABI differs from the System V ABI in that 8 byte structs  
are returned in registers EAX/EDX instead of passing the address of the  
struct on the stack. See: https://developer.apple.com/library/content/documentation/DeveloperTools/Conceptual/LowLevelABI/130-IA-32_Function_Calling_Conventions/IA32.html  
  
The current implementation for this platform crashes when compiled for  
32bit OSX (e.g. when running a 32bit iOS simulator) due to not  
satisfying the ABI.

---

## Comment 1

> Username: mjohns  
> Created_at: 2017-08-02 19:54:54 UTC  
> Url: https://github.com/boostorg/context/pull/64#issuecomment-319780230  

issue #62

---

## Comment 2

> Username: olk  
> Created_at: 2017-08-03 05:20:40 UTC  
> Url: https://github.com/boostorg/context/pull/64#issuecomment-319870623  

very nice, ty

---
