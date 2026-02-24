# #98 - Add Bjam support [Closed]

> Username: viboes  
> Created at: 2016-02-28 22:31:26 UTC  
> Updated at: 2016-03-07 00:00:28 UTC  
> Closed at: 2016-02-29 15:54:04 UTC  
> Url: https://github.com/boostorg/hof/issues/98  



---

## Comment 1

> Username: pfultz2  
> Created at: 2016-02-29 04:19:27 UTC  
> Updated at: 2016-02-29 04:19:36 UTC  
> Url: https://github.com/boostorg/hof/issues/98#issuecomment-190031277  

I added a Jamfile, following how other libraries have done it. How do I run the tests with it? When I run `bjam cxxflags="-std=c++11"` in the test directory, it tells me:  
  
```  
/usr/local/share/boost-build/src/build/project.jam:111: in load-parent from module project  
error: Could not find parent for project at '.'  
error: Did not find Jamfile.jam or Jamroot.jam in any parent directory.  
/usr/local/share/boost-build/src/build/project.jam:449: in initialize from module project  
/usr/local/share/boost-build/src/build/project.jam:301: in load-jamfile from module project  
/usr/local/share/boost-build/src/build/project.jam:64: in load from module project  
/usr/local/share/boost-build/src/build/project.jam:145: in project.find from module project  
/usr/local/share/boost-build/src/build-system.jam:535: in load from module build-system  
/usr/local/share/boost-build/src/kernel/modules.jam:289: in import from module modules  
/usr/local/share/boost-build/src/kernel/bootstrap.jam:139: in boost-build from module  
/usr/local/share/boost-build/boost-build.jam:8: in module scope from module  
```

---

## Comment 2

> Username: viboes  
> Created at: 2016-02-29 12:12:54 UTC  
> Url: https://github.com/boostorg/hof/issues/98#issuecomment-190188766  

You will need to add a Jamroot.jam  
You can try to copy the ones in https://github.com/viboes/std-make

---

## Comment 3

> Username: viboes  
> Created at: 2016-02-29 12:25:50 UTC  
> Url: https://github.com/boostorg/hof/issues/98#issuecomment-190191586  

You could also need a user-config.jam file on your home.  
Next follows some things I have in  
  
```  
import toolset : using ;   
using darwin : 5.2.0    : /usr/local/gcc-5.2.0/bin/g++-5.2.0 :   
    <lnkflags>-lssp   
    <cxxflags>"-fstack-protector-all -Wno-unused-local-typedefs -Wno-variadic-macros -Wno-deprecated-declarations"   
    ;  
using darwin : 5.2.0_11 : /usr/local/gcc-5.2.0/bin/g++-5.2.0 :   
    <lnkflags>-lssp <lnkflags>"-std=c++11"   
    <cxxflags>"-fstack-protector-all -std=c++11 -Wno-unused-local-typedefs -Wno-variadic-macros -Wno-unused-function -Wno-deprecated-declarations"   
    ;  
using darwin : 5.2.0_14 : /usr/local/gcc-5.2.0/bin/g++-5.2.0 :   
    <lnkflags>-lssp <lnkflags>"--coverage -std=c++14"   
    <cxxflags>"-fstack-protector-all -std=c++14 -Wno-unused-local-typedefs -Wno-variadic-macros -Wno-unused-function -Wno-deprecated-declarations"   
    ;  
using darwin : 5.2.0_1z : /usr/local/gcc-5.2.0/bin/g++-5.2.0 :   
    <lnkflags>-lssp <lnkflags>"--coverage -std=c++1z"   
    <cxxflags>"-fstack-protector-all -std=c++1z -Wno-unused-local-typedefs -Wno-variadic-macros -Wno-unused-function -Wno-deprecated-declarations"   
    ;  
  
#using clang : 3.5.0          : /Users/viboes/clang/3.5/build/Release+Asserts/bin/clang++ :  <cxxflags>-Wno-unused-value <cxxflags>-Wno-c99-extensions  <cxxflags>-Wno-variadic-macros ;  
#using clang : 3.5.0_11       : /Users/viboes/clang/3.5/build/Release+Asserts/bin/clang++ : <cxxflags>"-std=c++11 -stdlib=libc++ -I/Users/viboes/clang/3.5/build/Release+Asserts/include" <linkflags>"-std=c++11 -stdlib=libc++ -L/Users/viboes/clang/3.5/build/Release+Asserts/lib"  <cxxflags>-Wno-unused-value <cxxflags>-Wno-variadic-macros ;  
#using clang : 3.5.0_1y       : /Users/viboes/clang/3.5/build/Release+Asserts/bin/clang++ : <cxxflags>"-std=c++1y -stdlib=libc++ -I/Users/viboes/clang/3.5/build/Release+Asserts/include" <linkflags>"-std=c++1y -stdlib=libc++ -L/Users/viboes/clang/3.5/build/Release+Asserts/lib"  <cxxflags>-Wno-unused-value <cxxflags>-Wno-variadic-macros ;  
  
#using clang : 3.5.0_asan     : /Users/viboes/clang/3.5/build/Debug+Asserts/bin/clang++ ;  
#using clang : 3.5.0_11_asan  : /Users/viboes/clang/3.5/build/Debug+Asserts/bin/clang++ : <cxxflags>"-std=c++11 -stdlib=libc++ -I/Users/viboes/clang/3.5/build/Debug+Asserts/include -fsanitize=address" <linkflags>"-std=c++11 -stdlib=libc++ -L/Users/viboes/clang/3.5/build/Debug+Asserts/lib -fsanitize=address"  <cxxflags>-Wno-unused-value ;  
  
#using clang : 3.5.0_ubsan    : /Users/viboes/clang/3.5/build/Debug+Asserts/bin/clang++ ;  
#using clang : 3.5.0_11_ubsan : /Users/viboes/clang/3.5/build/Debug+Asserts/bin/clang++ : <cxxflags>"-std=c++11 -stdlib=libc++ -I/Users/viboes/clang/3.5/build/Debug+Asserts/include -fsanitize=undefined " <linkflags>"-std=c++11 -stdlib=libc++ -L/Users/viboes/clang/3.5/build/Debug+Asserts/lib -fsanitize=undefined"  <cxxflags>-Wno-unused-value ;  
  
#using clang : 3.5.0_tsan     : /Users/viboes/clang/3.5/build/Debug+Asserts/bin/clang++ ;  
#using clang : 3.5.0_11_tsan  : /Users/viboes/clang/3.5/build/Debug+Asserts/bin/clang++ : <cxxflags>"-std=c++11 -stdlib=libc++ -I/Users/viboes/clang/3.5/build/Debug+Asserts/include -fsanitize=thread -fno-omit-frame-pointer" <linkflags>"-std=c++11 -stdlib=libc++ -L/Users/viboes/clang/3.5/build/Debug+Asserts/lib -fsanitize=thread "  <cxxflags>-Wno-unused-value ;  
  
```  
  
Once you have that you call it using   
  
```  
cd test  
bjam toolset=clang-3.5.0_1y  
```

---

## Comment 4

> Username: pfultz2  
> Created at: 2016-02-29 15:32:14 UTC  
> Url: https://github.com/boostorg/hof/issues/98#issuecomment-190258075  

Ok now when I run `BOOST_ROOT=/usr/local/opt/boost bjam cxxflags="-std=c++11"`, I get:  
  
```  
Jamfile.v2:12: in test_all  
ERROR: rule "run" unknown in module "Jamfile</Users/paul/github/Fit/test>".  
Jamfile.v2:18: in modules.load  
```  
  
Where does the "run" rule come from?

---

## Comment 5

> Username: pfultz2  
> Created at: 2016-02-29 15:40:37 UTC  
> Url: https://github.com/boostorg/hof/issues/98#issuecomment-190261330  

Nevermind, I wasn't importing testing.

---

## Comment 6

> Username: pfultz2  
> Created at: 2016-02-29 15:54:04 UTC  
> Url: https://github.com/boostorg/hof/issues/98#issuecomment-190266532  

Alright, I got the tests running, thanks.

---

## Comment 7

> Username: viboes  
> Created at: 2016-02-29 22:53:55 UTC  
> Url: https://github.com/boostorg/hof/issues/98#issuecomment-190436613  

Great.
