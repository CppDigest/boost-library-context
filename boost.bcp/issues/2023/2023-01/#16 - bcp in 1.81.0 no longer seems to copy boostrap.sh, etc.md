# #16 - bcp in 1.81.0 no longer seems to copy boostrap.sh, etc. [Closed]

> Username: brucestephens  
> Created at: 2023-01-18 12:54:15 UTC  
> Updated at: 2023-02-12 11:45:15 UTC  
> Closed at: 2023-02-12 11:45:15 UTC  
> Url: https://github.com/boostorg/bcp/issues/16  

In previous releases (specifically 1.79.0 and before) I could do:  
  
    bcp build core system stacktrace /tmp/test   
  
and `/tmp/test` includes `bootstrap.sh`, `boost-build.jam`, and so on. All the infrastructure needed so I can just run `bootstrap.sh` and then `b2` to build the necessary libraries. But in 1.81.0 it does not. (I've no idea why: it looks like bcp itself is identical in the two versions.)

---

## Comment 1

> Username: Arne-W  
> Created at: 2023-02-03 16:19:33 UTC  
> Url: https://github.com/boostorg/bcp/issues/16#issuecomment-1416094514  

For me its the same.  
It also does not copy the ``Jamroot`` file which makes it impossible to build in a custom namespace.

---

## Comment 2

> Username: ErofeevK  
> Created at: 2023-02-12 08:42:04 UTC  
> Url: https://github.com/boostorg/bcp/issues/16#issuecomment-1426973499  

Indeed, there is no difference in the BCP source code between the two versions. However, the `build` module has undergone a change. BCP uses the file `tools/build/boost-build.jam` to trigger special (undiscoverable) dependencies. This file is no longer present in the Boost `build` module. A commit  (9388ae1e9524d3d4ed07a) in the `develop` branch of BCP fixes this issue by using a different file from the `build` module also it adds special dependencies for Boost.Graph. I have extracted the necessary changes for our CI build as a patch file. Here it is.  
[BCP_special_deps_9388ae1e9524d3d4ed07a.patch](https://github.com/boostorg/bcp/files/10715631/BCP_special_deps_9388ae1e9524d3d4ed07a.patch)

---

## Comment 3

> Username: jzmaddock  
> Created at: 2023-02-12 11:03:21 UTC  
> Url: https://github.com/boostorg/bcp/issues/16#issuecomment-1427002765  

Apologies for dropping the ball on this one, I'll merge to master once CI has cycled.

---

## Comment 4

> Username: jzmaddock  
> Created at: 2023-02-12 11:45:15 UTC  
> Url: https://github.com/boostorg/bcp/issues/16#issuecomment-1427011334  

Merged.  Should be fixed in master too now.
