# #447 - Test warning -- explicitly defaulted default constructor is implicitly deleted [Closed]

> Username: vddvss  
> Created at: 2019-04-28 20:26:44 UTC  
> Updated at: 2019-07-29 17:52:40 UTC  
> Closed at: 2019-07-29 17:52:40 UTC  
> Url: https://github.com/boostorg/hana/issues/447  

When compiling with Clang trunk, I get a `defaulted-function-deleted` warning for the `test.tuple.cnstr.trap` test (compiler output below).  
  
The constructor for `struct Foo` is defaulted:  
  
https://github.com/boostorg/hana/blob/9ffeb079fed195849d384fa7ed2f8dbcf9c04c88/test/tuple/cnstr.trap.cpp#L113  
  
`struct Foo` has a member `hana::tuple<Trap4> t;`, but the default constructor for `struct Trap4`  
  
https://github.com/boostorg/hana/blob/9ffeb079fed195849d384fa7ed2f8dbcf9c04c88/test/tuple/cnstr.trap.cpp#L59  
  
 is implicitly deleted, since the explicitly defaulted copy constructors and the templated rvalue constructor of `struct Trap4` implicitly delete the default constructor.   
  
If you'd like, I can do a PR to either remove or mark as deleted `struct Foos`'s default constructor or to explicitly define a default constructor for `struct Trap4`.   
   
Compiler output:  
  
```  
$ ninja test.tuple.cnstr.trap  
[1/2] Building CXX object test/CMakeFiles/test.tuple.cnstr.trap.dir/tuple/cnstr.trap.cpp.o  
FAILED: test/CMakeFiles/test.tuple.cnstr.trap.dir/tuple/cnstr.trap.cpp.o  
/home/colin/.local/lib64/llvm-installs/nightly/bin/clang++  -DBOOST_HANA_CONFIG_ENABLE_STRING_UDL -I../test/_include -I../include -fdiagnostics-color -ftemplate-backtrace-limit=0 -pedantic -Wall -Werror -Wextra -Wno-self-assign-overloaded -Wno-unused-local-typedefs -Wwrite-strings -Wno-gnu-string-literal-operator-template -std=c++14 -MD -MT test/CMakeFiles/test.tuple.cnstr.trap.dir/tuple/cnstr.trap.cpp.o -MF test/CMakeFiles/test.tuple.cnstr.trap.dir/tuple/cnstr.trap.cpp.o.d -o test/CMakeFiles/test.tuple.cnstr.trap.dir/tuple/cnstr.trap.cpp.o -c ../test/tuple/cnstr.trap.cpp  
../test/tuple/cnstr.trap.cpp:113:13: error: explicitly defaulted default constructor is implicitly deleted [-Werror,-Wdefaulted-function-deleted]  
            Foo() = default;  
            ^  
../test/tuple/cnstr.trap.cpp:116:32: note: default constructor of 'Foo' is implicitly deleted because field 't' has no default constructor  
            hana::tuple<Trap4> t;  
                               ^  
1 error generated.  
ninja: build stopped: subcommand failed.  
```

---

## Comment 1

> Username: ldionne  
> Created at: 2019-07-25 15:04:30 UTC  
> Url: https://github.com/boostorg/hana/issues/447#issuecomment-515081628  

Thanks for the report. I'm hitting the same problem.
