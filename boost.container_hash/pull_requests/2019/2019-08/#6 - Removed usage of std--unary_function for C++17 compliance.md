# #6 Removed usage of std::unary_function for C++17 compliance [Merged]

> Username: andrascii  
> Created at: 2019-08-09 09:16:20 UTC  
> Updated at: 2019-08-13 06:14:50 UTC  
> Merged at: 2019-08-13 06:14:50 UTC  
> Closed at: 2019-08-13 06:14:50 UTC  
> Url: https://github.com/boostorg/container_hash/pull/6  

Just to be able to compile boost with projects that uses C++17.

---

## Comment 1

> Username: mclow  
> Created_at: 2019-08-09 13:47:31 UTC  
> Url: https://github.com/boostorg/container_hash/pull/6#issuecomment-519925738  

The use of `unary_function` is already guarded by an `#ifdef`, removing it outright seems unnecessary.   It seems to be that it would be better to fix the conditional.

---

## Comment 2

> Username: mclow  
> Created_at: 2019-08-09 13:49:12 UTC  
> Url: https://github.com/boostorg/container_hash/pull/6#issuecomment-519926326  

from the Boost.Config docs:  
  
`BOOST_NO_CXX98_FUNCTION_BASE`: The standard library no longer supports `std::unary_function` and `std::binary_function`. They were deprecated in C++11 and is removed from C++14.

---

## Comment 3

> Username: jzmaddock  
> Created_at: 2019-08-09 17:41:40 UTC  
> Url: https://github.com/boostorg/container_hash/pull/6#issuecomment-520004709  

@mclow: this looks good to me now - corrects the PP logic as it should.

---

## Comment 4

> Username: mclow  
> Created_at: 2019-08-09 20:24:42 UTC  
> Updated_at: 2019-08-09 20:27:12 UTC  
> Url: https://github.com/boostorg/container_hash/pull/6#issuecomment-520052260  

Do we need the `_HAS_AUTO_PTR_ETC` checks at all?

---

## Comment 5

> Username: andrascii  
> Created_at: 2019-08-10 08:09:52 UTC  
> Url: https://github.com/boostorg/container_hash/pull/6#issuecomment-520129076  

> Do we need the `_HAS_AUTO_PTR_ETC` checks at all?  
  
Specifically in this part of code?  
`If _HAS_AUTO_PTR_ETC is defined to 0, std::auto_ptr and std::random_shuffle are not available.`  
  
What does this `_HAS_AUTO_PTR_ETC` have to do with this code?  
It looks like this macro was used to understand that `std::unary_function` was removed...

---

## Comment 6

> Username: jzmaddock  
> Created_at: 2019-08-10 08:36:54 UTC  
> Url: https://github.com/boostorg/container_hash/pull/6#issuecomment-520130820  

>Do we need the _HAS_AUTO_PTR_ETC checks at all?  
  
Probably not any more, they're msvc specific and have been superseded by BOOST_NO_CXX98_FUNCTION_BASE.

---

## Comment 7

> Username: andrascii  
> Created_at: 2019-08-10 15:20:01 UTC  
> Updated_at: 2019-08-10 15:20:25 UTC  
> Url: https://github.com/boostorg/container_hash/pull/6#issuecomment-520156896  

This solutions isn't working. Compiler still has an error about `std::unary_function`.  
Macro `BOOST_NO_CXX98_FUNCTION_BASE` isn't defined when I building the project.  
I use cmake and set:  
`set(CMAKE_CXX_STANDARD 17)`  
And I use clang.  
  
I still think that we need to just remove usage of std::unary_function.  
  
Any suggestions?

---

## Comment 8

> Username: jzmaddock  
> Created_at: 2019-08-11 07:35:13 UTC  
> Url: https://github.com/boostorg/container_hash/pull/6#issuecomment-520207182  

>This solutions isn't working. Compiler still has an error about std::unary_function.  
  
What's the clang version, platform and standard library?  We do test this in the Boost.Config tests so it _should_ all be working.  
  
BTW it would be wrong to simply remove all references to std::unary_function as that potentially breaks existing code.

---

## Comment 9

> Username: andrascii  
> Created_at: 2019-08-11 10:53:05 UTC  
> Url: https://github.com/boostorg/container_hash/pull/6#issuecomment-520218562  

> > This solutions isn't working. Compiler still has an error about std::unary_function.  
>   
> What's the clang version, platform and standard library? We do test this in the Boost.Config tests so it _should_ all be working.  
>   
> BTW it would be wrong to simply remove all references to std::unary_function as that potentially breaks existing code.  
  
clang version:  
Apple LLVM version 10.0.1 (clang-1001.0.46.4)  
Target: x86_64-apple-darwin18.7.0  
Thread model: posix  
InstalledDir: /Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin  
  
Compile flag -std=gnu++1z  also used

---

## Comment 10

> Username: jzmaddock  
> Created_at: 2019-08-11 12:10:50 UTC  
> Url: https://github.com/boostorg/container_hash/pull/6#issuecomment-520223151  

Well this is weird:  
  
* The last Config CI job tested Xcode-10 with the same compiler version as yours and passed in C++17 mode: https://travis-ci.org/boostorg/config/jobs/552215074  
* I notice that we do NOT currently define BOOST_NO_CXX98_FUNCTION_BASE for anything but msvc, but,  
* Looking at the current libc++ source std::unary_function etc are unconditionally defined: https://github.com/llvm-mirror/libcxx/blob/master/include/functional  
  
So maybe this is an Apple thing?  
  
So I guess the question is whether we should define BOOST_NO_CXX98_FUNCTION_BASE for any compiler claiming to be C++14 or later "just in case" ?

---

## Comment 11

> Username: andrascii  
> Created_at: 2019-08-11 21:27:08 UTC  
> Updated_at: 2019-08-11 21:27:41 UTC  
> Url: https://github.com/boostorg/container_hash/pull/6#issuecomment-520262066  

> So maybe this is an Apple thing?  
  
Maybe it is.  
But current solution for me is to define `BOOST_NO_CXX98_FUNCTION_BASE` on my own before including `boost/functional/hash.hpp` or just to avoid including this library.  
Because standard library that is shipped with Xcode does not contain the `std::unary_function` definition.

---

## Comment 12

> Username: mclow  
> Created_at: 2019-08-11 22:28:22 UTC  
> Url: https://github.com/boostorg/container_hash/pull/6#issuecomment-520266210  

> Because standard library that is shipped with Xcode does not contain the std::unary_function definition.  
  
This is very confusing to me. I don't have Xcode installed, but I do have the most recent Apple command-line tools installed, and the headers that ship with those `Apple LLVM version 10.0.1 (clang-1001.0.46.4)` definitely DO define `unary_function` on line 960 of `<utility>`.

---

## Comment 13

> Username: andrascii  
> Created_at: 2019-08-12 09:47:40 UTC  
> Url: https://github.com/boostorg/container_hash/pull/6#issuecomment-520361170  

> > Because standard library that is shipped with Xcode does not contain the std::unary_function definition.  
>   
> This is very confusing to me. I don't have Xcode installed, but I do have the most recent Apple command-line tools installed, and the headers that ship with those `Apple LLVM version 10.0.1 (clang-1001.0.46.4)` definitely DO define `unary_function` on line 960 of `<utility>`.  
  
Okey, I'll recheck this thing today/tomorrow.

---

## Comment 14

> Username: andrascii  
> Created_at: 2019-08-12 17:57:41 UTC  
> Updated_at: 2019-08-12 18:02:18 UTC  
> Url: https://github.com/boostorg/container_hash/pull/6#issuecomment-520529466  

> This is very confusing to me. I don't have Xcode installed, but I do have the most recent Apple command-line tools installed, and the headers that ship with those `Apple LLVM version 10.0.1 (clang-1001.0.46.4)` definitely DO define `unary_function` on line 960 of `<utility>`.  
  
I'm sorry, my mistake. I forgot to install patched library and regenerate my project using cmake.

---
