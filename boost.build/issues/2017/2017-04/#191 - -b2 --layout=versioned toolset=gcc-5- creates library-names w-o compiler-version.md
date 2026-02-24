# #191 - "b2 --layout=versioned toolset=gcc-5" creates library-names w/o compiler-version [Closed]

> Username: DenizThatMenace  
> Created at: 2017-04-06 16:16:51 UTC  
> Updated at: 2018-01-23 15:44:18 UTC  
> Closed at: 2018-01-23 15:44:18 UTC  
> Url: https://github.com/boostorg/build/issues/191  

Since **GCC** has switched its version scheme (starting with **GCC 5**), building the **Boost** libraries (with `b2`) with parameters `--layout=versioned` and `toolset=gcc-5` (or newer GCC) does **no longer create library-names containing the compiler's version number**. At least, this is the case on Linux.  
  
For example, building *Boost.Program_Options* with **`--layout=versioned`** results in:  
* **GCC 4.8**: `libboost_program_options-gcc48-mt-1_63_0.so`  
* **GCC 4.9**: `libboost_program_options-gcc49-mt-1_63_0.so`  
* **GCC 5**: `libboost_program_options-gcc-mt-1_63_0.so`  
* **GCC 6**: `libboost_program_options-gcc-mt-1_63_0.so`  
  
It would be great if this could be fixed so that the Boost-libraries build with GCC 5 and 6 could be installed side-by-side into the same directory.

---

## Comment 1

> Username: swatanabe  
> Created at: 2018-01-09 15:39:49 UTC  
> Url: https://github.com/boostorg/build/issues/191#issuecomment-356320519  

The reason is that the version regex in common.toolset-tag expects a two digit version.

---

## Comment 2

> Username: swatanabe  
> Created at: 2018-01-23 15:44:17 UTC  
> Url: https://github.com/boostorg/build/issues/191#issuecomment-359831462  

I used -dumpfullversion to bypass the problem.
