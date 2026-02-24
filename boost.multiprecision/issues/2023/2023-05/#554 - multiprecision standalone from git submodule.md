# #554 - multiprecision standalone from git submodule [Closed]

> Username: tcaduser  
> Created at: 2023-05-20 15:55:40 UTC  
> Updated at: 2023-05-20 18:08:06 UTC  
> Closed at: 2023-05-20 18:08:06 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/554  

Going from 1.80.0 to 1.82.0, I am no longer able to use the multiprecision library as standalone using it as a git module.  It appears I am expected to use a release download.  
  
How do I use the github source as a submodule in standalone mode?    
  
```  
error: #error "Boost.Config is considered a requirement to use Boost.Multiprecision in standalone mode. A package is provided at https://github.com/boostorg/multiprecision/releases"  
   21 | #    error "Boost.Config is considered a requirement to use Boost.Multiprecision in standalone mode. A package is provided at https://github.com/boostorg/multiprecision/releases"  
```  
  
I would like the convenience of being able to upgrade multiprecision by using specific submodule checkouts.

---

## Comment 1

> Username: jzmaddock  
> Created at: 2023-05-20 16:21:33 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/554#issuecomment-1555945573  

We've always required Boost.Config as well: that has no dependencies, so just grab the tarball of both Multiprecision and Config, and make sure *both* include directories are in your path and you should be good to go.  You can optionally add Math as well if you need floating point function support.

---

## Comment 2

> Username: tcaduser  
> Created at: 2023-05-20 16:27:23 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/554#issuecomment-1555946570  

Thanks.  I have it working again.  I just needed to add the boostorg/config  
submodule with the same release tag, as well as the appropriate include and  
I am good to go.  
  
On Sat, May 20, 2023 at 11:21 AM jzmaddock ***@***.***> wrote:  
  
> We've always required Boost.Config as well: that has no dependencies, so  
> just grab the tarball of both Multiprecision and Config, and make sure  
> *both* include directories are in your path and you should be good to go.  
> You can optionally add Math as well if you need floating point function  
> support.  
>  
> —  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/multiprecision/issues/554#issuecomment-1555945573>,  
> or unsubscribe  
> <https://github.com/notifications/unsubscribe-auth/AAS6BJIACATB4EDCPN2BGO3XHDVRPANCNFSM6AAAAAAYIZ4KRU>  
> .  
> You are receiving this because you authored the thread.Message ID:  
> ***@***.***>  
>
