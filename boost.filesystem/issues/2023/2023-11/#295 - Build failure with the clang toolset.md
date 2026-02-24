# #295 - Build failure with the clang toolset. [Closed]

> Username: matthijs  
> Created at: 2023-11-16 15:22:20 UTC  
> Updated at: 2023-11-16 15:37:44 UTC  
> Closed at: 2023-11-16 15:37:44 UTC  
> Url: https://github.com/boostorg/filesystem/issues/295  

I'm trying to build filesystem using the following command line:  
```bash  
./bootstrap.sh --with-toolset=clang --with-python=python3  
./b2 variant="release" cxxstd=20 stdlib=libc++ -j$(nproc)  
```  
  
I could reproduce this with the latest beta release of boost: https://boostorg.jfrog.io/artifactory/main/beta/1.84.0.beta1/source/  
  
Then I am getting all kinds of link failures (undefined symbols). See the full output here:  
https://gist.github.com/matthijs/f60a54d6f5ce86b3ec35c83d751c9631

---

## Comment 1

> Username: Lastique  
> Created at: 2023-11-16 15:37:44 UTC  
> Url: https://github.com/boostorg/filesystem/issues/295#issuecomment-1814702045  

I have already replied on the mailing list, so I'll just reiterate here.  
  
I consider this a clang/LLVM bug, and it should be reported [there](https://github.com/llvm/llvm-project/issues/). It is possible, that I have already reported this [here](https://github.com/llvm/llvm-project/issues/60578), although in my case the problem is with UBSAN compiler library.  
  
In any case, the compiler is expected to automatically link with its own libraries that define the standard symbols, unless asked otherwise on the command line (which Boost.Filesystem doesn't do).  
  
As a workaround, you may need to add explicit linking with compiler libraries on the command line. In particular, you may need to add `-lc++` and `-lc++abi` to your `linkflags` in `b2` command line.
