# #183 - Libatomic issues on macOS with Clang and CMake build [Closed]

> Username: Orphis  
> Created at: 2025-10-13 22:09:20 UTC  
> Updated at: 2025-10-14 12:52:18 UTC  
> Closed at: 2025-10-14 12:52:18 UTC  
> Url: https://github.com/boostorg/uuid/issues/183  

The CMake build files will try to link against `libatomic` on macOS if the compiler is `Clang` (not `AppleClang`) and the library is not used by libc++ on the platform to provide `<atomic>`.  
  
So the default is wrong and macOS users need to set `BOOST_UUID_LINK_LIBATOMIC=OFF` to get it building.  
  
It would be great if the CMake build files themselves checked dynamically (with `try_compile()`) if libatomic was needed on the platform to have a saner default everywhere without trying to add logic for every compiler and OS. An escape hatch is great to have, but only when doing something that is not standard, and I don't think it's asking for much to test Clang!  
  
Also, this type of issues would have been caught if tests were run with CMake and the Clang compiler, but as I understand they are only run with b2, are they not?

---

## Comment 1

> Username: pdimov  
> Created at: 2025-10-13 23:45:55 UTC  
> Url: https://github.com/boostorg/uuid/issues/183#issuecomment-3399424628  

We do run the tests on macOS, but with the default Clang (which is AppleClang.)  
  
E.g. https://github.com/boostorg/uuid/actions/runs/17858068917/job/50781657325

---

## Comment 2

> Username: pdimov  
> Created at: 2025-10-14 04:51:16 UTC  
> Url: https://github.com/boostorg/uuid/issues/183#issuecomment-3400104377  

Should be fixed by https://github.com/boostorg/uuid/commit/c933e32c2569e92ff601e8ff41c654a6e59f467d.  
  
Any tips on how would I go about testing LLVM Clang on macOS? How do you acquire it? How do you configure CMake to use it?

---

## Comment 3

> Username: Orphis  
> Created at: 2025-10-14 10:07:27 UTC  
> Url: https://github.com/boostorg/uuid/issues/183#issuecomment-3401070420  

Thanks for the quick fixes! The situation around libatomic isn't great, but I guess this is going to be a legacy workaround for a while, at least until this change makes it to various GCC releases: https://gcc.gnu.org/bugzilla/show_bug.cgi?id=81358. I'm not aware of an equivalent change in Clang to automatically link libatomic as needed.  
  
LLVM uses a more advanced check for libatomic and you could be doing something similar: https://github.com/llvm/llvm-project/blob/15d36fc72e176b1eed03a37efc9212202dd2b6cd/llvm/cmake/modules/CheckAtomic.cmake  
  
You want to check the LLVM releases on there: https://releases.llvm.org/  
It might take you to a tagged release: https://github.com/llvm/llvm-project/releases/tag/llvmorg-20.1.0  
And you can find the LLVM build there: https://github.com/llvm/llvm-project/releases/download/llvmorg-20.1.0/LLVM-20.1.0-macOS-ARM64.tar.xz  
  
Curiously, the latest release does not have a macOS build and an issue has been created some hours ago asking about it ( https://github.com/llvm/llvm-project/issues/163290 )  
  
To use it for a CMake build, you'll want to set CMAKE_C_COMPILER to the clang binary and CMAKE_CXX_COMPILER to clang++, either through command line (`cmake .... -DCMAKE_C_COMPILER=/.../clang++`) or in a toolchain file.  
  
Xcode should still be installed on the machine with Clang as it contains all the SDKs.

---

## Comment 4

> Username: pdimov  
> Created at: 2025-10-14 11:22:34 UTC  
> Url: https://github.com/boostorg/uuid/issues/183#issuecomment-3401319641  

For CI it's probably easier to use Homebrew. I'll try it out and see.

---

## Comment 5

> Username: pdimov  
> Created at: 2025-10-14 12:52:18 UTC  
> Url: https://github.com/boostorg/uuid/issues/183#issuecomment-3401658066  

Seems to have worked: https://github.com/boostorg/uuid/commit/d79210b0d8da733c75caa3cf31f8f7d11cfecb92
