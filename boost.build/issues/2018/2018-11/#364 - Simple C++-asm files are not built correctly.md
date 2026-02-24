# #364 - [cross-build] Simple C++/asm files are not built correctly [Open]

> Username: theodelrieu  
> Created at: 2018-11-09 09:53:14 UTC  
> Updated at: 2021-06-26 03:09:50 UTC  
> Url: https://github.com/boostorg/build/issues/364  

Hello,  
  
I opened an issue on [Boost.Context](https://github.com/boostorg/context/issues/76) a while ago, and I'm currently trying to solve it "properly".  
  
First, here is the small project I created to reproduce the issue:   
[boost_build_bug.zip](https://github.com/boostorg/build/files/2565353/boost_build_bug.zip)  
  
  
One of the problems is that the cross-compiling flags are not given to the compiler (I'm on macOS, and target iOS arm64/armv8):  
  
```bash  
$ b2 toolset=clang architecture=arm address-model=64 target-os=iphone asm -d+2  
```  
  
Here is the compiler invocation:  
  
>"clang++" -x assembler-with-cpp -fPIC -O0 -fno-inline -Wall -g    -c -o "bin/clang-darwin-10.0/debug/address-model-64/architecture-arm/target-os-iphone/make_arm64_aapcs_macho_gas.o" "make_arm64_aapcs_macho_gas.S"  
  
I would expect at least the `-arch arm64` flag. Trying to build the `cpp` target is no different in this case (this behaviour looks global, not specific to assembly files).  
  
I've read the user manual, and the [cross-building section](https://boostorg.github.io/build/manual/develop/index.html#bbv2.tasks.crosscompile) mentions the use of `user-config.jam`.  
Is this the only way to support cross-building?  
  
I do not mind writing a `user-config.jam`, but I find it confusing that there is no mapping between configuration flags (e.g. `architecture` + `address-model`) and compiler/linker flags (`-arch arm64` in this case).  
  
I'm a Boost.Build novice though, so I'm likely to have missed the point.

---

## Comment 1

> Username: stale[bot]  
> Created at: 2021-06-26 03:09:12 UTC  
> Url: https://github.com/boostorg/build/issues/364#issuecomment-868936375  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2  
This issue has been automatically marked as "transition" to indicate the potential for needing transition to the new B2 development project.
