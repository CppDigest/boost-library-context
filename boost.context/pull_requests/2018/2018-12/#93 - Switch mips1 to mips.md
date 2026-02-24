# #93 Switch mips1 to mips [Merged]

> Username: FlyGoat  
> Created at: 2018-12-02 05:12:27 UTC  
> Updated at: 2021-08-06 05:41:43 UTC  
> Merged at: 2021-08-06 05:41:37 UTC  
> Closed at: 2021-08-06 05:41:37 UTC  
> Url: https://github.com/boostorg/context/pull/93  



---

## Comment 1

> Username: FlyGoat  
> Created_at: 2018-12-02 05:14:28 UTC  
> Url: https://github.com/boostorg/context/pull/93#issuecomment-443482416  

These four PRs should be accepted together:  
- https://github.com/boostorg/build/pull/375  
- https://github.com/boostorg/config/pull/254  
- https://github.com/boostorg/context/pull/93  
- https://github.com/boostorg/log/pull/66

---

## Comment 2

> Username: FlyGoat  
> Created_at: 2018-12-02 05:16:00 UTC  
> Url: https://github.com/boostorg/context/pull/93#issuecomment-443482466  

And I need some suggestions on how to set default abi for mips as N64 when addressing-model is 64bit.

---

## Comment 3

> Username: olk  
> Created_at: 2018-12-02 11:17:14 UTC  
> Url: https://github.com/boostorg/context/pull/93#issuecomment-443499720  

In build/Jamfile something like:  
```  
local rule default_abi ( properties * )  
{  
    local tmp = sysv ;  
    if [ os.name ] = "NT" { tmp = ms ; }  
    else if [ os.name ] = "CYGWIN" { tmp = ms ; }  
    else if [ os.platform ] = "ARM" { tmp = aapcs ; }  
    else if ( [ os.platform ] = "MIPS" && <address-model>32 in $(properties) ) { tmp = o32 ; }  
    else if ( [ os.platform ] = "MIPS" && <address-model>64 in $(properties) ) { tmp = n64 ; }  
    return $(tmp) ;  
}  
```

---

## Comment 4

> Username: wzssyqa  
> Created_at: 2019-12-18 12:08:36 UTC  
> Url: https://github.com/boostorg/context/pull/93#issuecomment-567005733  

>   
>   
> In build/Jamfile something like:  
>   
> ```  
> local rule default_abi ( properties * )  
> {  
>     local tmp = sysv ;  
>     if [ os.name ] = "NT" { tmp = ms ; }  
>     else if [ os.name ] = "CYGWIN" { tmp = ms ; }  
>     else if [ os.platform ] = "ARM" { tmp = aapcs ; }  
>     else if ( [ os.platform ] = "MIPS" && <address-model>32 in $(properties) ) { tmp = o32 ; }  
>     else if ( [ os.platform ] = "MIPS" && <address-model>64 in $(properties) ) { tmp = n64 ; }  
  
I have a try, while properties is empty here.  
  
>     return $(tmp) ;  
> }  
> ```

---

## Comment 5

> Username: olk  
> Created_at: 2021-07-13 18:49:19 UTC  
> Url: https://github.com/boostorg/context/pull/93#issuecomment-879319250  

This PR is incomlete - it requires assembler for N64.  
What's is the status of MIPS unification?

---

## Comment 6

> Username: FlyGoat  
> Created_at: 2021-07-19 02:18:30 UTC  
> Url: https://github.com/boostorg/context/pull/93#issuecomment-882181349  

@olk What's meant by unification?

---

## Comment 7

> Username: FlyGoat  
> Created_at: 2021-07-19 02:22:22 UTC  
> Url: https://github.com/boostorg/context/pull/93#issuecomment-882183001  

Ohhh I see I was doing that, it was never completed.

---

## Comment 8

> Username: olk  
> Created_at: 2021-07-19 04:18:15 UTC  
> Url: https://github.com/boostorg/context/pull/93#issuecomment-882224244  

Because it was never completed - should I close this PR?

---

## Comment 9

> Username: FlyGoat  
> Created_at: 2021-07-21 02:08:14 UTC  
> Url: https://github.com/boostorg/context/pull/93#issuecomment-883829247  

Cc: @wzssyqa for suggestion.

---

## Comment 10

> Username: wzssyqa  
> Created_at: 2021-07-22 01:46:55 UTC  
> Url: https://github.com/boostorg/context/pull/93#issuecomment-884604870  

> Cc: @wzssyqa for suggestion.  
  
here is a dirty patch from Debian:  
https://sources.debian.org/patches/boost1.74/1.74.0-9/fix_mipsel_asm_files.patch/

---

## Comment 11

> Username: wzssyqa  
> Created_at: 2021-07-22 03:36:08 UTC  
> Url: https://github.com/boostorg/context/pull/93#issuecomment-884635600  

I noticed that on i386-userland on amd64-kernel, the OSPLAT value is X86.  
while mipsel-userland on mips64 kernel, the OSPLAT value is MIPS64.  
  
where the value of  
      static module_t root;  
from?

---

## Comment 12

> Username: wzssyqa  
> Created_at: 2021-07-22 05:34:37 UTC  
> Updated_at: 2021-07-22 05:37:51 UTC  
> Url: https://github.com/boostorg/context/pull/93#issuecomment-884666431  

```  
Index: boost1.74-1.74.0/tools/build/src/engine/jam.h  
===================================================================  
--- boost1.74-1.74.0.orig/tools/build/src/engine/jam.h  
+++ boost1.74-1.74.0/tools/build/src/engine/jam.h  
@@ -416,11 +416,12 @@  
 #endif  
  
 #ifdef __mips__  
-  #if defined(_ABI64)  
+  #if defined(_ABI64) && _MIPS_SIM == _ABI64  
     #define OSPLAT "OSPLAT=MIPS64"  
-  #elif defined(_ABIO32)  
+  #elif defined(_ABIO32) && _MIPS_SIM == _ABIO32  
     #define OSPLAT "OSPLAT=MIPS32"  
   #endif  
+  //TODO: support N32  
 #endif  
```  
  
This patch can make it workable with mips32 with mips64 kernel.

---

## Comment 13

> Username: olk  
> Created_at: 2021-07-22 06:42:59 UTC  
> Url: https://github.com/boostorg/context/pull/93#issuecomment-884689815  

> ```  
> Index: boost1.74-1.74.0/tools/build/src/engine/jam.h  
> ===================================================================  
> --- boost1.74-1.74.0.orig/tools/build/src/engine/jam.h  
> +++ boost1.74-1.74.0/tools/build/src/engine/jam.h  
> @@ -416,11 +416,12 @@  
>  #endif  
>   
>  #ifdef __mips__  
> -  #if defined(_ABI64)  
> +  #if defined(_ABI64) && _MIPS_SIM == _ABI64  
>      #define OSPLAT "OSPLAT=MIPS64"  
> -  #elif defined(_ABIO32)  
> +  #elif defined(_ABIO32) && _MIPS_SIM == _ABIO32  
>      #define OSPLAT "OSPLAT=MIPS32"  
>    #endif  
> +  //TODO: support N32  
>  #endif  
> ```  
>   
> This patch can make it workable with mips32 with mips64 kernel.  
  
Could you provide a Pull Request for boost.build, please?

---

## Comment 14

> Username: wzssyqa  
> Created_at: 2021-07-22 07:10:35 UTC  
> Url: https://github.com/boostorg/context/pull/93#issuecomment-884701114  

> > ```  
> > Index: boost1.74-1.74.0/tools/build/src/engine/jam.h  
> > ===================================================================  
> > --- boost1.74-1.74.0.orig/tools/build/src/engine/jam.h  
> > +++ boost1.74-1.74.0/tools/build/src/engine/jam.h  
> > @@ -416,11 +416,12 @@  
> >  #endif  
> >   
> >  #ifdef __mips__  
> > -  #if defined(_ABI64)  
> > +  #if defined(_ABI64) && _MIPS_SIM == _ABI64  
> >      #define OSPLAT "OSPLAT=MIPS64"  
> > -  #elif defined(_ABIO32)  
> > +  #elif defined(_ABIO32) && _MIPS_SIM == _ABIO32  
> >      #define OSPLAT "OSPLAT=MIPS32"  
> >    #endif  
> > +  //TODO: support N32  
> >  #endif  
> > ```  
> >   
> >   
> >       
> >         
> >       
> >   
> >         
> >       
> >   
> >       
> >     
> > This patch can make it workable with mips32 with mips64 kernel.  
>   
> Could you provide a Pull Request for boost.build, please?  
  
https://github.com/boostorg/build/commit/b97746e29cb1f4dc4cfd02bb6191441dbc0d39d8  
  
This has been fixed.

---

## Comment 15

> Username: olk  
> Created_at: 2021-07-22 07:20:42 UTC  
> Url: https://github.com/boostorg/context/pull/93#issuecomment-884705983  

> This has been fixed.  
  
And what is the TODO list for boost.context?

---

## Comment 16

> Username: wzssyqa  
> Created_at: 2021-07-22 07:37:24 UTC  
> Url: https://github.com/boostorg/context/pull/93#issuecomment-884713856  

> > This has been fixed.  
>   
> And what is the TODO list for boost.context?  
  
No real problem here, I guess.  
and now we use `mips1` for all mips revision.  
  
So should we switch `mips1` to `mips`?  
This effects some boost projects.

---

## Comment 17

> Username: olk  
> Created_at: 2021-08-06 05:41:43 UTC  
> Url: https://github.com/boostorg/context/pull/93#issuecomment-894015508  

ty

---
