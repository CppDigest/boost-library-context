# #306 - Support clang toolset-version option, as clang++-6.0 has no alternative symlinks [Open]

> Username: jeking3  
> Created at: 2018-05-13 10:32:45 UTC  
> Updated at: 2021-06-26 03:10:12 UTC  
> Url: https://github.com/boostorg/build/issues/306  

On Ubuntu Bionic when you install the package `clang++6` you get a compiler aptly named `clang++-6.0`.  There is no Ubuntu alternative that links `clang++` to `clang++-6.0` installed, nor can I find a package that would do that.  
  
The bjam gcc toolset selection supports version detection, i.e. `<toolset>gcc-6` will attempt to use the most recent compiler starting with the name `g++-6` - or `toolset=gcc-6.2` will specifically use `g++-6.2`.  This logic is embedded into the file `src/tools/gcc.jam`.  The corresponding `clang.jam` file is much less complex, and sadly also much less capable, while it would seem that the version selection code currently inside the init rule inside `gcc.jam` should be considered generic to all `toolset=` processing.  
  
There is a debian source package called `llvm-defaults` which contains a readme file that explains there will be no distribution provided defaults for llvm or clang versioning:  
  
```  
How to build with a specific version of clang?  
----------------------------------------------  
  
Please give the CC or CXX variables to the build system.  
For example, to build with clang-6.0, the following command should  
do the work:  
  
CC=clang-6.0 CXX=clang++-6.0 make  
or  
CC=clang-6.0 CXX=clang++-6.0 ./configure  
  
llvm-*/... are not handled using alternatives  
----------------------------------------------  
  
The symlinks in /usr/bin (llvm-*, ...) are not handled using the Debian  
alternative mechanism. There are differences in the architecture specific  
ABI on some architectures. Having the symlinks managed by alternatives  
doesn't allow reliable builds with the same major/minor version of the  
compiler.  
  
Feedback appreciated  
--------------------  
  
Feedback about this document is appreciated; preferably as a Severity:  
wishlist bug against Package: llvm . For general discussions and  
questions, subscribe and/or email the pkg-llvm-team@lists.alioth.debian.org  
mailing list.  
  
Maintainers of these packages  
-----------------------------  
  
Matthias Klose <doko@debian.org>  
Sylvestre Ledru <sylvestre@debian.org>  
```  
  
I would propose that the version selection logic currently in the `gcc.jam` get refactored out so that it can be shared, and that will simplify the gcc jam implementation as well as enhance the clang jam implementation.  This will allow for `toolset=clang-6` to find `clang++-6.0` as the compiler without a user-supplied `user-config.jam` file.

---

## Comment 1

> Username: stale[bot]  
> Created at: 2021-06-26 03:09:30 UTC  
> Url: https://github.com/boostorg/build/issues/306#issuecomment-868936469  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2  
This issue has been automatically marked as "transition" to indicate the potential for needing transition to the new B2 development project.
