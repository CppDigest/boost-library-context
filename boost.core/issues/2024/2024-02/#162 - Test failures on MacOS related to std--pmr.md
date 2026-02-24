# #162 - [string_view] Test failures on MacOS related to std::pmr [Closed]

> Username: Lastique  
> Created at: 2024-02-06 14:45:02 UTC  
> Updated at: 2024-02-07 21:10:33 UTC  
> Closed at: 2024-02-07 21:10:33 UTC  
> Url: https://github.com/boostorg/core/issues/162  

See here, for example:  
  
https://github.com/boostorg/core/actions/runs/7799837421/job/21271466543#step:6:15992  
  
```  
dyld[40635]: Symbol not found: __ZNSt3__13pmr20get_default_resourceEv  
  Referenced from: <78D9D57E-6A40-31D9-B0ED-8D8C1EC12D2E> /Users/runner/work/core/core/boost-root/bin.v2/libs/core/test/sv_construct_test.test/clang-darwin-15/debug/cxxstd-17-iso/threading-multi/visibility-hidden/sv_construct_test  
  Expected in:     <C39B7907-EABB-3019-AAB0-F6AEA01D5C1A> /usr/lib/libc++.1.dylib  
  
EXIT STATUS: 134  
```  
  
This appeared only recently, I'm not sure what causes it. My guess is something has changed in Mac OS GHA images.

---

## Comment 1

> Username: pdimov  
> Created at: 2024-02-06 15:15:37 UTC  
> Url: https://github.com/boostorg/core/issues/162#issuecomment-1930018911  

This changed: https://github.com/actions/runner-images/commit/87666d8f324f2ea7e649fa5be1d60aa0730edcdc

---

## Comment 2

> Username: pdimov  
> Created at: 2024-02-06 20:21:33 UTC  
> Url: https://github.com/boostorg/core/issues/162#issuecomment-1930688898  

Should be fixed with https://github.com/boostorg/core/commit/2f092c62fe93f725ea57bb785d5aa1dc6ab9b1ce, although GHA seems to have switched back to an older macos-13 image so it's hard to tell.
