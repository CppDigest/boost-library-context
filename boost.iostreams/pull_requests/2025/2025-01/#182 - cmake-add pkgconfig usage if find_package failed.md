# #182 cmake:add pkgconfig usage if find_package failed [Open]

> Username: Arniiiii  
> Created at: 2025-01-16 14:54:54 UTC  
> Updated at: 2025-01-16 14:54:54 UTC  
> Url: https://github.com/boostorg/iostreams/pull/182  

It's related to the situation with zstd: it has homebrew makefiles as default buildsystem. Thus, they use .pc files. Yes, they have CMake support, but downstream distros, like Gentoo, use Meson, which doesn't produce .cmake config files. Thus, I've added the mechanism for looking for a .pc files of corresponding libraries.

---
