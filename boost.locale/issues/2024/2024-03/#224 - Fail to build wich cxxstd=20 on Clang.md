# #224 - Fail to build wich cxxstd=20 on Clang [Closed]

> Username: sdebionne  
> Created at: 2024-03-09 08:28:42 UTC  
> Updated at: 2025-01-12 16:39:29 UTC  
> Closed at: 2025-01-12 16:39:27 UTC  
> Url: https://github.com/boostorg/locale/issues/224  

We are having trouble building Boost.Locale for Conda forge (see https://github.com/conda-forge/boost-feedstock/pull/194) with `cxxstd=20` which is required for the Cobalt library, on osx (clang 16).  
  
I am not familiar with Locale, is there any macro that we should set to prevent the following issue:  
  
```  
Undefined symbols for architecture x86_64:  
  "typeinfo for char8_t", referenced from:  
      std::__1::basic_string<char8_t, std::__1::char_traits<char8_t>, std::__1::allocator<char8_t> > boost::locale::ios_info::string_set::get<char8_t>() const in formatter.o  
ld: symbol(s) not found for architecture x86_64  
clang-16: error: linker command failed with exit code 1 (use -v to see invocation)  
```

---

## Comment 1

> Username: Flamefire  
> Created at: 2024-03-09 10:21:31 UTC  
> Url: https://github.com/boostorg/locale/issues/224#issuecomment-1986817536  

That is a known issue with Clang. Which version of clang and Boost are you using? In Boost 1.84 a [workaround](https://github.com/boostorg/locale/commit/908b2744b4d9ad95da5f274ffad78d4c22087fd4) is implemented:  
https://github.com/boostorg/locale/blob/de51c1a14244a8941f88e4ee9c2b6a742b362b90/include/boost/locale/config.hpp#L93  
  
In Clang 15 this should be fixed.

---

## Comment 2

> Username: sdebionne  
> Created at: 2024-03-09 11:44:50 UTC  
> Url: https://github.com/boostorg/locale/issues/224#issuecomment-1986834157  

Thanks for the quick answer. We build Boost1.84 with Clang 16 on MacOs 10.9. I am not sure if the clang version on MacOs matches the LLVM's once though.

---

## Comment 3

> Username: Flamefire  
> Created at: 2024-03-09 12:37:25 UTC  
> Url: https://github.com/boostorg/locale/issues/224#issuecomment-1986845842  

They should match the LLVM 15.x version for a while already. So I'm not sure how to fix this from Boosts side. I'd say it should be fine to define `BOOST_LOCALE_NO_CXX20_STRING8` on your side for this. If you have any information when/where this issue is fixed on MacOS and how to improve the condition to detect that I'd be grateful. Otherwise I'll just disable `u8string` support for MacOS completely.

---

## Comment 4

> Username: h-vetinari  
> Created at: 2024-03-10 07:52:21 UTC  
> Url: https://github.com/boostorg/locale/issues/224#issuecomment-1987133958  

We're on clang 16 in conda-forge (though we could use clang 17 and soon 18 if we want; however libcxx is stuck at 16 pending some infrastructure work). The macOS deployment target in the PR mentioned by @sdebionne was already bumped to 10.13, and if there's a hard requirement to go higher, we can do that.  
  
W.r.t.  
https://github.com/boostorg/locale/blob/de51c1a14244a8941f88e4ee9c2b6a742b362b90/include/boost/locale/config.hpp#L93  
  
I don't see how that issue is still relevant; it was fixed as of LLVM 15, and we're past that.

---

## Comment 5

> Username: h-vetinari  
> Created at: 2024-03-10 07:53:19 UTC  
> Url: https://github.com/boostorg/locale/issues/224#issuecomment-1987134198  

> I am not sure if the clang version on MacOs matches the LLVM's once though.  
  
There's only the clang we provide ourselves, we never use the system(==image) compilers in conda-forge.

---

## Comment 6

> Username: Flamefire  
> Created at: 2024-03-10 10:59:14 UTC  
> Url: https://github.com/boostorg/locale/issues/224#issuecomment-1987185154  

> I don't see how that issue is still relevant; it was fixed as of LLVM 15, and we're past that.  
  
Looks I misinterpreted the error message which also started with "Undefined symbols" and seemingly related to `std::basic_string<char8_t>` and I missed the important part in all those templates: "typeinfo for char8_t" is what is missing.  
  
And indeed I've seen that before popping up with libc++ on FreeBSD. I fixed that for the upcoming release. You can use that or apply https://github.com/boostorg/locale/commit/c5e8f02c903696a213fc4b710f6740ccd1f07f4e as a patch.

---

## Comment 7

> Username: Flamefire  
> Created at: 2025-01-12 16:39:27 UTC  
> Url: https://github.com/boostorg/locale/issues/224#issuecomment-2585815457  

Fixed by https://github.com/boostorg/locale/pull/212
