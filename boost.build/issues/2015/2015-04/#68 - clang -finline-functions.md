# #68 - clang -finline-functions [Closed]

> Username: mnbittorrent  
> Created at: 2015-04-13 18:14:31 UTC  
> Updated at: 2015-04-14 09:00:36 UTC  
> Closed at: 2015-04-14 09:00:36 UTC  
> Url: https://github.com/boostorg/build/issues/68  

With clang 3.6, there is a new warning for the -finline-functions flag. This should be removed from clang settings.  
  
```  
clang: warning: optimization flag '-finline-functions' is not supported  
clang: warning: argument unused during compilation: '-finline-functions'  
```  
  
See http://stackoverflow.com/questions/26108606/no-support-to-finline-functions-in-clang-3-5  
  
```  
clang --version  
Apple LLVM version 6.1.0 (clang-602.0.49) (based on LLVM 3.6.0svn)  
Target: x86_64-apple-darwin14.1.0  
Thread model: posix  
```

---

## Comment 1

> Username: vprus  
> Created at: 2015-04-13 19:10:28 UTC  
> Updated at: 2015-04-13 19:10:54 UTC  
> Url: https://github.com/boostorg/build/issues/68#issuecomment-92465572  

Is this not already solved in https://github.com/boostorg/build/commit/c2529b667ada26efd5ac320903012ef20efc119e ?

---

## Comment 2

> Username: mnbittorrent  
> Created at: 2015-04-13 19:32:08 UTC  
> Url: https://github.com/boostorg/build/issues/68#issuecomment-92471506  

Not quite. The same thing will have to be done for clang-darwin.jam instead of clang-linux.jam.

---

## Comment 3

> Username: vprus  
> Created at: 2015-04-14 09:00:36 UTC  
> Url: https://github.com/boostorg/build/issues/68#issuecomment-92705437  

True. Done in d20dad8
