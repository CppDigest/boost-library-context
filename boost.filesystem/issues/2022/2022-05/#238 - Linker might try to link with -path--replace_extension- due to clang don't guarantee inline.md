# #238 - Linker might try to link with "path::replace_extension" due to clang don't guarantee inline [Closed]

> Username: thelastlin  
> Created at: 2022-05-25 13:46:08 UTC  
> Updated at: 2022-05-25 14:22:00 UTC  
> Closed at: 2022-05-25 14:22:00 UTC  
> Url: https://github.com/boostorg/filesystem/issues/238  

Boost version: 1.79.0  
Platform: Apple macOS 12.4  
Compiler: Apple Clang 13.1.6 (Provided by Xcode or cmdline tools)  
  
-----  
  
Details:  
1. Build and install `boost_filesystem`(v1.79) with prefix `/usr/local`.  
2. Build some code, for example, [rime/librime](https://www.github.com/rime/librime), and then ld complains that it unable to find the symbol of `boost::filesystem::path::replace_extension`.  
4. Replace  `boost_filesystem` v1.79 with v1.78, rebuild the project, works fine.  
  
------  
  
Noticed that the following code uses `BOOST_FORCEINLINE`, ...  
https://github.com/boostorg/filesystem/blob/97722a3107d136eebe9425804fe0d13eab3ce2bc/include/boost/filesystem/path.hpp#L704  
  
... which has been defined at `config/detail/suffix.hpp` :  
https://github.com/boostorg/config/blob/08dced51e9a688ec3e9c1b7c5f90c99f2b1cdca7/include/boost/config/detail/suffix.hpp#L618  
  
But Clang does not guarantee that inline substitution actually occurs ([Clang documents](https://releases.llvm.org/13.0.0/tools/clang/docs/AttributeReference.html#always-inline-force-inline)).

---

## Comment 1

> Username: Lastique  
> Created at: 2022-05-25 14:22:00 UTC  
> Url: https://github.com/boostorg/filesystem/issues/238#issuecomment-1137319593  

Most probably, you're building against Boost.Filesystem headers that do not match the Boost.Filesystem binary. In 1.78, `path::replace_extension` was an exported function, in 1.79 it is not (it is an inline function in the header).  
  
`BOOST_FORCEINLINE` is irrelevant. Whether the compiler does actually inline the function or not, the function is available and should be present to the linker. The linker error indicates that the function is not present in the header for inlining and not present in the built library for linking either.
