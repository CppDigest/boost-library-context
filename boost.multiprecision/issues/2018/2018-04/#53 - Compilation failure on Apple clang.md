# #53 - Compilation failure on Apple clang [Closed]

> Username: NAThompson  
> Created at: 2018-04-24 13:18:24 UTC  
> Updated at: 2018-05-12 07:23:25 UTC  
> Closed at: 2018-05-12 07:23:25 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/53  

```  
clang-darwin.compile.c++ ../../../bin.v2/libs/multiprecision/test/git_issue_30.test/clang-darwin-4.2.1/debug/git_issue_30.o  
git_issue_30.cpp:28:6: error: call to constructor of 'E' is ambiguous  
   E x1(r);  // triggers explicit conversion operator.  
     ^  ~  
git_issue_30.cpp:9:8: note: candidate is the implicit move constructor  
struct E  
       ^  
git_issue_30.cpp:9:8: note: candidate is the implicit copy constructor  
git_issue_30.cpp:11:4: note: candidate constructor  
   E(boost::multiprecision::cpp_rational const&)  
   ^  
1 error generated.  
  
    "clang++" -x c++ -std=c++14 -fPIC -m64 -O0 -fno-inline -Wall -g -fsanitize=address -DBOOST_ALL_NO_LIB=1 -I"../../.." -I"../include" -c -o "../../../bin.v2/libs/multiprecision/test/git_issue_30.test/clang-darwin-4.2.1/debug/git_issue_30.o" "git_issue_30.cpp"  
  
...failed clang-darwin.compile.c++ ../../../bin.v2/libs/multiprecision/test/git_issue_30.test/clang-darwin-4.2.1/debug/git_issue_30.o...  
...failed updating 1 target...  
```  
  
Clang version:  
  
```  
 clang++ --version  
Apple LLVM version 9.1.0 (clang-902.0.39.1)  
Target: x86_64-apple-darwin17.5.0  
Thread model: posix  
InstalledDir: /Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin  
```
