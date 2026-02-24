# #288 - Error after including boost/thread/detail/platform_time.hpp [Open]

> Username: kkumar45  
> Created at: 2019-07-12 06:49:58 UTC  
> Updated at: 2019-09-14 13:08:43 UTC  
> Url: https://github.com/boostorg/thread/issues/288  

The header `#include <boost/thread/detail/platform_time.hpp>` has been added in Boost 1.69.0.   
It includes another header `#include <mach/mach_time.h>` which causes conflict on MAC OS when used with `#include <llvm/Support/MachO.h>`. Conflicts are for various identifier which are already defined in MachO.h.   
  
Error :   
In the error below `vm_prot.h `gets included by `mach_time.h` (seen by generating preprocessor output)  
  
```  
llvm/Support/MachO.h:527:7: error: expected identifier  
      VM_PROT_READ    = 0x1,  
      ^  
/Applications/Xcode10.1.0.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX10.14.sdk/usr/include/mach/vm_prot.h:83:22: note: expanded from macro 'VM_PROT_READ'  
#define VM_PROT_READ    ((vm_prot_t) 0x01)      /* read permission */  
```  
  
Sample program when compiled on MAC, generates the same error:  
  
```  
#include <boost/thread/detail/platform_time.hpp>  
#include <llvm/Support/MachO.h>  
  
int main()  
{  
    return 0;  
}  
```

---

## Comment 1

> Username: kkumar45  
> Created at: 2019-07-30 11:43:00 UTC  
> Url: https://github.com/boostorg/thread/issues/288#issuecomment-516383520  

Can someone reproduce it?

---

## Comment 2

> Username: viboes  
> Created at: 2019-08-26 05:25:17 UTC  
> Url: https://github.com/boostorg/thread/issues/288#issuecomment-524720287  

Sorry, i have no accesos to machine.  
  
could you provide a pr?

---

## Comment 3

> Username: kkumar45  
> Created at: 2019-08-27 08:43:32 UTC  
> Updated at: 2019-08-27 08:43:54 UTC  
> Url: https://github.com/boostorg/thread/issues/288#issuecomment-525203475  

@viboes Did you mean pull request containing fix for the issue? Could you please elaborate if you meant something else.

---

## Comment 4

> Username: viboes  
> Created at: 2019-09-09 19:33:16 UTC  
> Url: https://github.com/boostorg/thread/issues/288#issuecomment-529631606  

Yes a pull request
