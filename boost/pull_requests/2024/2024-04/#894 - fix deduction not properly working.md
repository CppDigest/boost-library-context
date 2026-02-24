# #894 fix deduction not properly working [Merged]

> Username: grisumbras  
> Created at: 2024-04-29 06:00:40 UTC  
> Updated at: 2024-05-09 15:01:59 UTC  
> Merged at: 2024-04-30 22:44:20 UTC  
> Closed at: 2024-04-30 22:44:20 UTC  
> Url: https://github.com/boostorg/boost/pull/894  

This should finally fix bfgroup/b2#368.  
  
Background. After my b2 PR (bfgroup/b2#375) was merged, the build system is properly creating two alternatives for checked libraries (e.g. `/openssl//ssl`): before the deduced properties are added, and after that. What this **does not** result in, though, is build system actually compiling and linking corresponding files twice. This is because `deduced-X` are _hidden_ features, and they do not affect the target path. As a result `foo.o` with `address-model=32` , and `foo.o` with `address-model=32 deduced-architecture=x86` result in the same file (e.g. `gcc-13/debug/address-model-32/threading-multi/visibility-hidden/foo.o`, notice the lack of `architecture`).  
  
After some thinking, I decided that this is a flaw in how deduced platform features are implemented. They should not be hidden, because their presence affects binary compatibility.  
  
This change removes `deduced-X` features, as their only purpose was hiding the related property from the path. Instead it fully relies on conditional property adding the deduced values for `X` feature if necessary.  
  
**NOTE:** the unfortunate result of this change is that all targets in a Boost build will get `architecture-A` and `address-model-M` parts in the path.

---

## Comment 1

> Username: pdimov  
> Created_at: 2024-04-29 12:07:26 UTC  
> Updated_at: 2024-04-29 12:07:41 UTC  
> Url: https://github.com/boostorg/boost/pull/894#issuecomment-2082558580  

That's probably the right thing to do, but it has the potential of breaking previously working builds on Windows due to path limits.  
  
I'm not sure whether there's anything we can do about that, though.

---

## Comment 2

> Username: pdimov  
> Created_at: 2024-04-29 12:09:00 UTC  
> Url: https://github.com/boostorg/boost/pull/894#issuecomment-2082561472  

(References https://github.com/bfgroup/b2/pull/375).

---

## Comment 3

> Username: pdimov  
> Created_at: 2024-05-01 11:11:20 UTC  
> Url: https://github.com/boostorg/boost/pull/894#issuecomment-2088301051  

Yeah, I'm hitting Windows path limits when building with MinGW-w64:  
  
```  
...failed gcc.compile.c++ ..\..\bin.v2\libs\uuid\test\~hdr-decl-detail-basic_name_generator~hpp.test\gcc-8\release\address-model-64\architecture-x86\cxxstd-14-iso\threadapi-win32\threading-multi\visibility-hidden\~hdr-decl-detail-basic_name_generator~hpp.o...  
gcc.compile.c++ ..\..\bin.v2\libs\uuid\test\~hdr-decl-detail-basic_name_generator~hpp.test\gcc-8\release\address-model-64\architecture-x86\cxxstd-1z-iso\threadapi-win32\threading-multi\visibility-hidden\~hdr-decl-detail-basic_name_generator~hpp.o  
Assembler messages:  
Fatal error: can't create ..\..\bin.v2\libs\uuid\test\~hdr-decl-detail-basic_name_generator~hpp.test\gcc-8\release\address-model-64\architecture-x86\cxxstd-1z-iso\threadapi-win32\threading-multi\visibility-hidden\~hdr-decl-detail-basic_name_generator~hpp.o: No such file or directory  
```  
  
If we keep this, https://github.com/bfgroup/b2/issues/378 will be urgently required. Or we need to figure out a way to enable `--abbreviate-paths` on the Boost side if B2 doesn't cooperate.

---

## Comment 4

> Username: pdimov  
> Created_at: 2024-05-01 11:19:44 UTC  
> Url: https://github.com/boostorg/boost/pull/894#issuecomment-2088307987  

Another option is to special-case `architecture` and `address-model` and instead of adding `address-model-64/architecture-x86/` to the path, just add `x86-64/` to it.

---

## Comment 5

> Username: pdimov  
> Created_at: 2024-05-01 22:37:28 UTC  
> Url: https://github.com/boostorg/boost/pull/894#issuecomment-2089243947  

https://github.com/boostorg/boost/pull/898

---
