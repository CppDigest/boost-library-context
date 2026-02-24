# #371 - Revisit Intel Compiler Support? [Open]

> Username: mrks  
> Created at: 2017-11-20 21:28:41 UTC  
> Updated at: 2022-10-21 03:47:20 UTC  
> Url: https://github.com/boostorg/hana/issues/371  

The [compiler support matrix](https://github.com/boostorg/hana/wiki/General-notes-on-compiler-support) says that icc is not supported because it lacks *C++14 variable templates*. Since September, [those are said to be supported by Intel](https://software.intel.com/en-us/articles/c14-features-supported-by-intel-c-compiler). How does that change things?

---

## Comment 1

> Username: ldionne  
> Created at: 2017-11-25 19:16:26 UTC  
> Updated at: 2017-11-25 19:17:38 UTC  
> Url: https://github.com/boostorg/hana/issues/371#issuecomment-346960204  

Unfortunately, I don't have access to ICC. It's tricky to provide support for a compiler without having first-hand access to it and adding it to the CI, since any future change could break that support silently.

---

## Comment 2

> Username: badair  
> Created at: 2017-11-26 06:58:06 UTC  
> Url: https://github.com/boostorg/hana/issues/371#issuecomment-346988829  

@ldionne They do grant licenses for FOSS development upon request, but the latest version is likely not anywhere close to compiling the Hana test suite. I also doubt that there exist potentially reasonable workarounds for Hana to use. Metaprogramming with Intel's compiler is not fun (ask @brunocodutra), although I have spent admittedly little time with the latest version.

---

## Comment 3

> Username: brunocodutra  
> Created at: 2017-11-26 10:55:36 UTC  
> Url: https://github.com/boostorg/hana/issues/371#issuecomment-346999702  

In my experience ICC is terrible at SFINAE in particular, which might not be as big of a deal for Hana as it is for Metal, so maybe it's worth it giving it a try.

---

## Comment 4

> Username: bchiodo  
> Created at: 2020-01-07 07:36:04 UTC  
> Url: https://github.com/boostorg/hana/issues/371#issuecomment-571471962  

@ldionne I don’t know when this change happened, but the Intel compiler is now freely available in the [Intel System Studio](https://software.intel.com/en-us/system-studio/choose-download), even for commercial purposes.  
  
Could ICC support be revisited now?

---

## Comment 5

> Username: ldionne  
> Created at: 2022-10-21 03:47:20 UTC  
> Url: https://github.com/boostorg/hana/issues/371#issuecomment-1286421004  

I definitely have interest in Hana supporting ICC. However, I have no bandwidth to do it and I don't foresee that happening. If you want to try out Hana on ICC and send PRs, I'll be happy to look at them when I can.
