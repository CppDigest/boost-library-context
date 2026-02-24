# #153 Fix Apple clang version detection false positives. [Merged]

> Username: saurik  
> Created at: 2019-08-20 08:34:49 UTC  
> Updated at: 2019-10-20 17:01:20 UTC  
> Merged at: 2019-10-20 17:01:20 UTC  
> Closed at: 2019-10-20 17:01:20 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/153  

In number.hpp, a workaround is presented for old versions of Apple clang. Apple uses non-standard version numbers, which makes it difficult to rely on ```__clang_major__```. The code first checked ```__APPLE_CC__``` in an attempt to check "is this compiler using Apple's version numbering", but open source upstream clang versions also define this variable when compiling to Apple targets; as this workaround breaks some client code, this makes some codebases impossible to compile for Apple targets with upstream clang. The correct variable to check is ```__apple_build_version__```.

---

## Comment 1

> Username: saurik  
> Created_at: 2019-10-20 05:42:13 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/153#issuecomment-544222743  

This bug is still here :(. I've updated my patch. Can this please get merged?

---

## Comment 2

> Username: jzmaddock  
> Created_at: 2019-10-20 17:01:13 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/153#issuecomment-544270873  

Thanks, CI failures are my fault (changes to Boost.Math).  Will merge now...

---
