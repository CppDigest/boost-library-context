# #3 - property_tree not included? [Closed]

> Username: nilsnolde  
> Created at: 2025-10-28 09:44:14 UTC  
> Updated at: 2025-10-28 13:19:48 UTC  
> Closed at: 2025-10-28 13:19:48 UTC  
> Url: https://github.com/boostorg/headers/issues/3  

Over at vcpkg, `boost-headers` target doesn't seem to include `boost/property_tree`. I was just porting to the this new target to avoid compiled libs, but it couldn't those headers. [It seems](https://github.com/microsoft/vcpkg/blob/master/ports/boost-headers/portfile.cmake) that it's not vcpkg related, but I couldn't figure out in this repo how things are installed 😅

---

## Comment 1

> Username: pdimov  
> Created at: 2025-10-28 11:27:25 UTC  
> Url: https://github.com/boostorg/headers/issues/3#issuecomment-3455986450  

Have you installed property_tree using vcpkg?

---

## Comment 2

> Username: nilsnolde  
> Created at: 2025-10-28 12:55:36 UTC  
> Url: https://github.com/boostorg/headers/issues/3#issuecomment-3456345528  

Yep, I needed to install it explicitly, that worked of course.

---

## Comment 3

> Username: pdimov  
> Created at: 2025-10-28 13:00:23 UTC  
> Url: https://github.com/boostorg/headers/issues/3#issuecomment-3456361613  

`headers` is a "library" that doesn't do anything, except give you the correct include path. It doesn't depend on the rest of the header-only libraries (that's 120+ libraries or something like that), so they don't get installed when you install `boost-headers`.

---

## Comment 4

> Username: nilsnolde  
> Created at: 2025-10-28 13:17:19 UTC  
> Updated at: 2025-10-28 13:19:46 UTC  
> Url: https://github.com/boostorg/headers/issues/3#issuecomment-3456422433  

Ah, thanks, I thought there's some magic going with the Jamfile (no clue about those instructions). I figured that vcpkg "meta-package" would install all header-only libs for me. What doesn't quite fit: we make heavy use of other boost header-only libs like boost/geometry, and it didn't complain there (I also didn't set it explicitly). Could be a transitive dependency from some other stuff though which is probably the case then.. Sorry, I don't actually have access to a local run, it's all done in GHA.
