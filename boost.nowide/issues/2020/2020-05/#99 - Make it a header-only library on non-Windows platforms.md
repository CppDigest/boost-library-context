# #99 - Make it a header-only library on non-Windows platforms. [Closed]

> Username: Berrysoft  
> Created at: 2020-05-22 15:01:45 UTC  
> Updated at: 2020-05-22 15:13:00 UTC  
> Closed at: 2020-05-22 15:13:00 UTC  
> Url: https://github.com/boostorg/nowide/issues/99  

I just cannot see why it is *not* header-only on *nix platforms.

---

## Comment 1

> Username: Flamefire  
> Created at: 2020-05-22 15:04:06 UTC  
> Url: https://github.com/boostorg/nowide/issues/99#issuecomment-632740585  

Because it wouldn't work with `--std=c++xx` option as there are non-standard functions used, especially the environment modifying ones.     
Besides that it requires a compiled library on Windows, so for the sake of cross-platform development it is more reasonable to always have a compiled library instead of introducing additional conditionals in the build systems of the users

---

## Comment 2

> Username: Berrysoft  
> Created at: 2020-05-22 15:13:00 UTC  
> Url: https://github.com/boostorg/nowide/issues/99#issuecomment-632745698  

OK, it seems fair...
