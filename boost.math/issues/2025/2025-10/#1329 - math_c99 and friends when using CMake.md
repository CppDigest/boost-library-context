# #1329 - math_c99 and friends when using CMake [Open]

> Username: philippremy  
> Created at: 2025-10-18 15:52:25 UTC  
> Updated at: 2025-10-31 12:08:41 UTC  
> Url: https://github.com/boostorg/math/issues/1329  

Maybe I am missing something, but building `Boost.Math` (or Boost in general) with CMake causes the components of `math_c99` and friends to be missing. I don't see the libraries in `CMakeLists.txt`. Is that intended?  
  
When I build with `b2` the libraries are build and the CMake components are available...  
  
EDIT:  
  
(Re-)Adding support for these libraries in CMake appears to be quite trivial. I could provide a PR for that if required and wanted.

---

## Comment 1

> Username: mborland  
> Created at: 2025-10-20 12:51:01 UTC  
> Url: https://github.com/boostorg/math/issues/1329#issuecomment-3421931257  

Is this something you actually need? It was never added because I couldn't imagine someone using CMake also needed this backwards compatibility.

---

## Comment 2

> Username: philippremy  
> Created at: 2025-10-20 21:03:35 UTC  
> Url: https://github.com/boostorg/math/issues/1329#issuecomment-3423711450  

Well, no, at least not personally...  
  
But apparently some vcpkg stuff relies/relied on math_c99 to be available (I have no idea why): https://github.com/microsoft/vcpkg/pull/38728  
  
If you asked me, I would put some documentation somewhere which indicates that the CMake build behaves a little differently than b2. Or is that common sense in the Boost world? I honestly don't know 😅.   
Or we put the compiled libraries behind a special switch in CMake (something like `BOOST_MATH_ENABLE_LEGACY`)...

---

## Comment 3

> Username: jzmaddock  
> Created at: 2025-10-21 08:56:28 UTC  
> Url: https://github.com/boostorg/math/issues/1329#issuecomment-3425499504  

We should find a way to include this as a target in the CMake file, I wouldn't even make it conditional on a macro as it's not deprecated, just not used much, so a separate target, or maybe even a separate CMake file?  My apologies, but my ignorance of all things CMake is showing ;)

---

## Comment 4

> Username: mborland  
> Created at: 2025-10-21 09:07:41 UTC  
> Url: https://github.com/boostorg/math/issues/1329#issuecomment-3425538284  

> We should find a way to include this as a target in the CMake file, I wouldn't even make it conditional on a macro as it's not deprecated, just not used much, so a separate target, or maybe even a separate CMake file? My apologies, but my ignorance of all things CMake is showing ;)  
  
The easiest way would be to take the `.patch` file out of the Vcpkg build which adds this support. I don't think I agree with building them by default. I expect that suddenly going from a header only library to a compiled library will cause issues downstream. Vcpkg requires a macro to build these legacy bits.

---

## Comment 5

> Username: jzmaddock  
> Created at: 2025-10-21 11:56:17 UTC  
> Url: https://github.com/boostorg/math/issues/1329#issuecomment-3426228305  

> The easiest way would be to take the .patch file out of the Vcpkg build which adds this support. I don't think I agree with building them by default. I expect that suddenly going from a header only library to a compiled library will cause issues downstream. Vcpkg requires a macro to build these legacy bits.  
  
They would be separate targets (there are 6 of them and none are called "boost_math"), as I said I know nothing about CMake, but I would not expect any target depending on "boost_math" to cause them to build in that case?  If you're installing Boost.Math via CMake, then they really should be built anyway IMO, just as they are via b2.

---

## Comment 6

> Username: philippremy  
> Created at: 2025-10-30 16:17:21 UTC  
> Url: https://github.com/boostorg/math/issues/1329#issuecomment-3468841981  

> > The easiest way would be to take the .patch file out of the Vcpkg build which adds this support. I don't think I agree with building them by default. I expect that suddenly going from a header only library to a compiled library will cause issues downstream. Vcpkg requires a macro to build these legacy bits.  
>   
> They would be separate targets (there are 6 of them and none are called "boost_math"), as I said I know nothing about CMake, but I would not expect any target depending on "boost_math" to cause them to build in that case? If you're installing Boost.Math via CMake, then they really should be built anyway IMO, just as they are via b2.  
  
I agree. Imo that would not change the header-only math library. That can be used without modifications, just as before. But if someone (for whatever reason) requires the legacy modules and can't use `b2`, they would still be available...  
  
The point is: Using `b2` on macOS is somewhat painful. Currently, `bootstrap.sh` does *not* work with a Homebrew LLVM/Clang, because it links to the wrong C++ standard library (that obviously is an issue in itself, see https://github.com/Homebrew/homebrew-core/issues/235411). That can be fixed by simply adding a `-L<LLVM_LIB_PATH>` to LDFLAGS. But `bootstrap.sh` does not allow any custom `CXXFLAGS` or `LDFLAGS`. So I would need my project's users to uninstall LLVM completely if they want to build Boost with the legacy libraries :(. In CMake, I could just pass the additional flags and if I recall correctly, that wasn't even required there (maybe CMake already passes the correct search paths).

---

## Comment 7

> Username: mborland  
> Created at: 2025-10-31 07:41:07 UTC  
> Url: https://github.com/boostorg/math/issues/1329#issuecomment-3471653760  

Boost is in the middle of release cycle, but once things are ready for the next version I will make these changes.

---

## Comment 8

> Username: philippremy  
> Created at: 2025-10-31 12:08:41 UTC  
> Url: https://github.com/boostorg/math/issues/1329#issuecomment-3472799687  

Thank you :). That helps me out a lot!
