# #316 - b2 headers fails when trying to build tag 1.61.0 on msvc-12.0 [Closed]

> Username: brandon-kohn  
> Created at: 2018-06-15 13:35:00 UTC  
> Updated at: 2018-06-15 13:39:27 UTC  
> Closed at: 2018-06-15 13:39:27 UTC  
> Url: https://github.com/boostorg/build/issues/316  

I'm trying to build 1.61 after checking out the tag (for 1.61). I've updated submodules etc.  
  
Here's what I see when I issue the b2:  
  
```  
E:\Projects\modular-boost [tag_boost-1.61.0]> ./b2 headers  
Performing configuration checks  
  
    - 32-bit                   : yes  
    - arm                      : no  
    - mips1                    : no  
    - power                    : no  
    - sparc                    : no  
    - x86                      : yes  
    - symlinks supported       : yes  
error: Cannot create link boost\functional\hash\extensions.hpp to libs\functional\include\boost\functional\hash\extensions.hpp.  
error: Link previously defined to another file, libs\container_hash\include\boost\functional\hash\extensions.hpp.  
  
E:\Projects\modular-boost [tag_boost-1.61.0]>  
```

---

## Comment 1

> Username: brandon-kohn  
> Created at: 2018-06-15 13:36:11 UTC  
> Url: https://github.com/boostorg/build/issues/316#issuecomment-397622209  

Note: there is no `e:\Projects\modular-boost\boost` directory when I call this. So I don't see how there can be a previously defined link.

---

## Comment 2

> Username: swatanabe  
> Created at: 2018-06-15 13:37:43 UTC  
> Url: https://github.com/boostorg/build/issues/316#issuecomment-397622648  

AMDG  
  
On 06/15/2018 07:35 AM, Brandon Kohn wrote:  
> I'm trying to build 1.61 after checking out the tag (for 1.61). I've updated submodules etc.  
>   
> <snip>  
> error: Cannot create link boost\functional\hash\extensions.hpp to libs\functional\include\boost\functional\hash\extensions.hpp.  
> error: Link previously defined to another file, libs\container_hash\include\boost\functional\hash\extensions.hpp.  
>   
  
container_hash is not part of 1.61.  You'll need to  
remove it to get rid of the conflict.  
  
In Christ,  
Steven Watanabe

---

## Comment 3

> Username: brandon-kohn  
> Created at: 2018-06-15 13:39:27 UTC  
> Url: https://github.com/boostorg/build/issues/316#issuecomment-397623083  

Thanks Steven. That did the trick.
