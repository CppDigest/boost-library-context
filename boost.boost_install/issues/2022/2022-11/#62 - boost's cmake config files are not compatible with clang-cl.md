# #62 - boost's cmake config files are not compatible with clang-cl [Open]

> Username: Febbe  
> Created at: 2022-11-08 12:20:01 UTC  
> Updated at: 2022-11-16 20:37:12 UTC  
> Url: https://github.com/boostorg/boost_install/issues/62  

On Windows, clang-cl is compatible for compiling and linking against MSVC libraries.  
In this case with MSVC compiled boost. Therefore, the cmake-config file should not report "NOTFOUND" if I actually want MSVC compiled boost libraries to be linked. Boost should also generally detect if clang is running in compatibility mode for msvc and simulate a build as with msvc as well.

---

## Comment 1

> Username: pdimov  
> Created at: 2022-11-15 23:21:30 UTC  
> Url: https://github.com/boostorg/boost_install/issues/62#issuecomment-1316004899  

We require an exact toolset match in the general case, even though some toolsets are link-compatible. For example, libraries built with VS2019 can be used from VS2022, but we still require an exact match. The way to link to the libraries you want is by setting the Boost_COMPILER variable to e.g. "vc143", analogously to how FindBoost.cmake works.

---

## Comment 2

> Username: Febbe  
> Created at: 2022-11-16 11:28:45 UTC  
> Url: https://github.com/boostorg/boost_install/issues/62#issuecomment-1316845739  

FindBoost.cmake is managed by cmake right? Because, they provide the possibility to check against multiple tool chains, when they are provided via `Boost_COMPILER`. But as soon I specify multiple it tries to use the specified clang15 Toolchain, which does not exist on all build systems. But actually the 143 and 142 targets are available and compatible. So when I specify clang15, clang14, vc143, vc142 it should choose vc143 when it is available and the others are not.

---

## Comment 3

> Username: pdimov  
> Created at: 2022-11-16 11:35:57 UTC  
> Url: https://github.com/boostorg/boost_install/issues/62#issuecomment-1316857234  

You're right that we don't support multiple toolchains in Boost_COMPILER. We probably should.

---

## Comment 4

> Username: pdimov  
> Created at: 2022-11-16 20:37:12 UTC  
> Url: https://github.com/boostorg/boost_install/issues/62#issuecomment-1317638038  

Boost_COMPILER can be a list now, at least on the develop branch, via https://github.com/boostorg/boost_install/commit/534727e8a0583d4fb57d943a6e417f989c4189ba. I don't know yet if that will go into the 1.81 release, because it's already in beta.
