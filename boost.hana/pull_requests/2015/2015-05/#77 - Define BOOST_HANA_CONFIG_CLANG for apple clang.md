# #77 [Config] Define BOOST_HANA_CONFIG_CLANG for apple clang [Closed]

> Username: KholdStare  
> Created at: 2015-05-25 23:21:31 UTC  
> Updated at: 2015-05-26 21:30:47 UTC  
> Closed at: 2015-05-26 21:30:47 UTC  
> Url: https://github.com/boostorg/hana/pull/77  

Hi!  
  
I had trouble building the library with the version of clang that came with Xcode, and it came down to the configuration thinking I was using GCC. Using `__apple_build_version__` to map to the proper clang build version fixed the problems. See https://gist.github.com/yamaya/2924292 for version reference.  
  
Thank you!  
Alex.

---

## Comment 1

> Username: ldionne  
> Created_at: 2015-05-26 06:45:47 UTC  
> Url: https://github.com/boostorg/hana/pull/77#issuecomment-105418034  

Can you please show me your `clang++ --version`? Mine completely fails to build Hana (compilation errors and then ICEs when I fix the compilation errors).  
  
Here's my `clang++ --version`:  
  
``` shell  
> clang++ --version  
Apple LLVM version 6.0 (clang-600.0.57) (based on LLVM 3.5svn)  
Target: x86_64-apple-darwin13.4.0  
Thread model: posix  
```

---

## Comment 2

> Username: KholdStare  
> Created_at: 2015-05-26 15:24:44 UTC  
> Url: https://github.com/boostorg/hana/pull/77#issuecomment-105566250  

Thanks for taking a look.  
  
Here is my `clang++ --version` (I updated Xcode on OS X Yosemite):  
  
```  
> clang++ --version  
Apple LLVM version 6.1.0 (clang-602.0.53) (based on LLVM 3.6.0svn)  
Target: x86_64-apple-darwin14.3.0  
Thread model: posix  
```  
  
Note that I actually had to also specify a path for libc++ when running cmake:   
  
`cmake .. -DCMAKE_CXX_COMPILER=/usr/bin/clang -DLIBCXX_ROOT=/Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/include/c++/v1/`

---

## Comment 3

> Username: ldionne  
> Created_at: 2015-05-26 16:16:15 UTC  
> Url: https://github.com/boostorg/hana/pull/77#issuecomment-105584132  

I'm on Mavericks with Xcode 6.2, and I can tell that the associated Clang isn't able to compile Hana. However, it works on your Xcode 6.3.2 (the latest on Yosemite). Based on [the Xcode/Clang version record you linked above](https://gist.github.com/yamaya/2924292), it would be nice to know whether Xcode 6.3 and 6.3.1 are able to compile Hana too, which would give us the minimal required version of Xcode.

---

## Comment 4

> Username: ldionne  
> Created_at: 2015-05-26 17:42:07 UTC  
> Url: https://github.com/boostorg/hana/pull/77#issuecomment-105615078  

I tweaked your PR in 66c0b70; do you agree with my approach? Also, could you please build 66c0b70 with your Xcode just to make sure it actually works. Of course, I'll get my hands on a more recent version of Xcode to make sure it works on the long term, but for now I don't have that.

---

## Comment 5

> Username: KholdStare  
> Created_at: 2015-05-26 18:29:11 UTC  
> Url: https://github.com/boostorg/hana/pull/77#issuecomment-105625791  

It looks good to me, and everything compiles. Thank you! :smiley:

---

## Comment 6

> Username: ldionne  
> Created_at: 2015-05-26 19:21:41 UTC  
> Url: https://github.com/boostorg/hana/pull/77#issuecomment-105640575  

Thanks for the feedback and the PR! This issue will be closed automatically when I merge the changes to master, which I will do as soon as https://travis-ci.org/ldionne/hana/builds/64145010 passes. Thanks!

---
