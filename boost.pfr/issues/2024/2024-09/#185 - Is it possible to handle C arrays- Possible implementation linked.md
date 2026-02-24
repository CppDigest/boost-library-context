# #185 - Is it possible to handle C arrays? Possible implementation linked [Open]

> Username: quicknir  
> Created at: 2024-09-23 15:59:25 UTC  
> Updated at: 2024-10-19 16:41:43 UTC  
> Url: https://github.com/boostorg/pfr/issues/185  

There's another library called `repr` which claims to handle C arrays properly: https://godbolt.org/z/vbz639n4d. Can this solution be used to handle C arrays properly in boost pfr?  
  
Here's a link to the implementation as it appears in the source tree (rather than amalgamated): https://github.com/Tsche/repr/blob/master/include/librepr/reflection/detail/arity.h

---

## Comment 1

> Username: Tsche  
> Created at: 2024-09-23 18:54:24 UTC  
> Url: https://github.com/boostorg/pfr/issues/185#issuecomment-2369110878  

This defect was one of the reasons I wrote my own library :P  
  
Related:  
https://github.com/boostorg/pfr/issues/16  
https://github.com/boostorg/pfr/issues/20  
https://github.com/boostorg/pfr/issues/162  
https://github.com/boostorg/pfr/issues/169  
https://github.com/boostorg/pfr/issues/170

---

## Comment 2

> Username: p00f  
> Created at: 2024-09-27 10:47:13 UTC  
> Url: https://github.com/boostorg/pfr/issues/185#issuecomment-2378991598  

The fix in #170 works, I've tested it with giant multiply-nested structs

---

## Comment 3

> Username: apolukhin  
> Created at: 2024-10-17 09:00:34 UTC  
> Url: https://github.com/boostorg/pfr/issues/185#issuecomment-2418966092  

Solution from #170 breaks multiple tests. It requires some additional tweaking, I'd appreciate a PR.  
  
As a quick workaround you could replace native C array with std::array

---

## Comment 4

> Username: p00f  
> Created at: 2024-10-17 09:13:56 UTC  
> Url: https://github.com/boostorg/pfr/issues/185#issuecomment-2418996073  

std::array is not an option because the structs containing the arrays are from headers i don't control  
  
I'm willing to fix the tests but don't have much experience with template metaprogramming, would it be fine if I give it a try and you can guide me in the PR?

---

## Comment 5

> Username: p00f  
> Created at: 2024-10-17 09:54:36 UTC  
> Url: https://github.com/boostorg/pfr/issues/185#issuecomment-2419083095  

Also, how do I run the tests?

---

## Comment 6

> Username: apolukhin  
> Created at: 2024-10-19 13:00:12 UTC  
> Url: https://github.com/boostorg/pfr/issues/185#issuecomment-2423835372  

> would it be fine if I give it a try and you can guide me in the PR?  
  
Yes, I'll do my best  
  
> Also, how do I run the tests?  
  
I usually checkout the whole Boost with submodules https://github.com/boostorg/boost  
  
Then run the `./bootstrap.sh`  
  
After that   
```  
cd libs/pfr/test  
../../../b2 cxxstd=17 toolset=clang-19  
```

---

## Comment 7

> Username: p00f  
> Created at: 2024-10-19 13:32:11 UTC  
> Url: https://github.com/boostorg/pfr/issues/185#issuecomment-2423854551  

> cxxstd=17  
  
don't you need 20 for this `names_as_array` and friends?  
  
>  toolset=clang-19  
  
my distro only has llvm 18  
  
regardless, i'll try and see if this works

---

## Comment 8

> Username: p00f  
> Created at: 2024-10-19 14:22:13 UTC  
> Url: https://github.com/boostorg/pfr/issues/185#issuecomment-2423914160  

https://github.com/boostorg/pfr/pull/189
