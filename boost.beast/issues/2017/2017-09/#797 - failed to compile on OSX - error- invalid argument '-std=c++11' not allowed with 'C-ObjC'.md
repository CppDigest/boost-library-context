# #797 - failed to compile on OSX : error: invalid argument '-std=c++11' not allowed with 'C/ObjC' [Closed]

> Username: SSE4  
> Created at: 2017-09-26 09:04:41 UTC  
> Updated at: 2017-10-02 21:13:16 UTC  
> Closed at: 2017-10-02 21:13:16 UTC  
> Url: https://github.com/boostorg/beast/issues/797  

### Version of Beast  
  
commit dca65932a8055dd3e240633c6a058db8aa7f7915 (HEAD, origin/master)  
Author: Vinnie Falco <vinnie.falco@gmail.com>  
Date:   Tue Sep 12 17:16:07 2017 -0700  
  
    Set version to 116  
  
### Steps necessary to reproduce the problem  
  
`./b2 libs/beast/test cxxstd=11`  
  
### All relevant compiler information  
  
* [out.txt](https://github.com/boostorg/beast/files/1332431/out.txt)  
* 10.12.6 Sierra, Xcode 8.3.3  
* bash-3.2$ clang++ --version  
* Apple LLVM version 8.1.0 (clang-802.0.42)  
* Target: x86_64-apple-darwin16.7.0  
* Thread model: posix  
* InstalledDir:   
* /Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin  
* bash-3.2$ g++ --version  
* Configured with: --prefix=/Applications/Xcode.app/Contents/Developer/usr --with-gxx-include-* dir=/usr/include/c++/4.2.1  
* Apple LLVM version 8.1.0 (clang-802.0.42)  
* Target: x86_64-apple-darwin16.7.0  
* Thread model: posix  
* InstalledDir:   
* /Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-09-26 15:23:03 UTC  
> Url: https://github.com/boostorg/beast/issues/797#issuecomment-332234475  

Confirmed

---

## Comment 2

> Username: SSE4  
> Created at: 2017-09-27 05:40:51 UTC  
> Url: https://github.com/boostorg/beast/issues/797#issuecomment-332414596  

fixed by https://github.com/boostorg/build/commit/bf2acd4f29a1458c4256bd9b0e73644ef5aaa652

---

## Comment 3

> Username: vinniefalco  
> Created at: 2017-10-02 21:13:16 UTC  
> Url: https://github.com/boostorg/beast/issues/797#issuecomment-333666950  

I'll close this since it looks resolved
