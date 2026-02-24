# #26 - virtual_access_multi.cpp failed in clang on Windows [Closed]

> Username: yuxianch  
> Created at: 2019-11-28 03:40:23 UTC  
> Updated at: 2020-03-04 15:25:43 UTC  
> Closed at: 2020-03-04 15:25:43 UTC  
> Url: https://github.com/boostorg/contract/issues/26  

I was using clang-cl on Windows(VS2019) to run the contract tests, and I found [virtual_access_multi.cpp](https://github.com/boostorg/contract/blob/develop/test/public_function/virtual_access_multi.cpp) failed in running.  
The compiling and linking commands looks like this.  
```  
clang-cl "..\..\..\libs\system\src\error_code.cpp" -TP /Z7 /Od /Ob0 /W3 /GR /MDd /EHs /std:c++17 -D_CRT_USE_BUILTIN_OFFSETOF -c -DBOOST_ALL_NO_LIB=1 -DBOOST_SYSTEM_DYN_LINK=1 -I"..\..\.."  
clang-cl "..\..\..\libs\contract\src\contract.cpp" -TP /Z7 /Od /Ob0 /W3 /GR /MDd /EHs /std:c++17 -D_CRT_USE_BUILTIN_OFFSETOF -c -DBOOST_ALL_NO_LIB=1 -DBOOST_SYSTEM_DYN_LINK=1 -I"..\..\.."  
clang-cl "public_function\virtual_access_multi.cpp" -TP /Z7 /Od /Ob0 /W3 /GR /MDd /EHs /std:c++17 -D_CRT_USE_BUILTIN_OFFSETOF -c -DBOOST_ALL_NO_LIB=1 -DBOOST_SYSTEM_DYN_LINK=1 -I"..\..\.."  
link /nologo /DEBUG /subsystem:console /out:"public_function.exe" error_code.obj contract.obj virtual_access_multi.obj  
```  
After that, "public_function.exe" was executed and it can be seen that the part related with `g` function is different with expected result.  
```  
--- running result  
+++ expected result  
@@ -11,31 +11,20 @@  
 a::inv  
 b::f::old  
 b::f::post  
 c::f::old  
 c::f::post  
 a::f::post  
-(((b)))::g::pre  
-b::g::old  
-b::g::body  
-b::g::post  
-b::g::body  
-c::inv  
+(((c)))::inv  
 a::inv  
-b::g::body  
-b::g::pre  
-b::g::old  
-b::g::body  
-b::g::post  
+c::g::pre  
 c::g::old  
 a::g::old  
 a::g::body  
-b::g::body  
 c::inv  
 a::inv  
-b::g::body  
 c::g::old  
 c::g::post  
 a::g::post  
 c::inv  
 a::inv  
 c::h::pre  
```  
I am not clear with the reason. Could someone explain? Thanks a lot!

---

## Comment 1

> Username: lcaminiti  
> Created at: 2019-11-29 04:04:54 UTC  
> Updated at: 2019-11-29 04:06:33 UTC  
> Url: https://github.com/boostorg/contract/issues/26#issuecomment-559652706  

Can you please do the followings?  
  
1. Re-run this test using BJam and send me the entire output of this:  
    > cd your-boost-dir\libs\contract\test  
    > your-boost-dir\bjam public_function-virtual_access_multi  
  
2. Run all tests using Bjam and send me the entire output of this:  
    > cd your-boost-dir\libs\contract\test  
    > your-boost-dir\bjam   
  
3. Send me the full version of Clang that you are using (output of `clang-cl --version`, or something like that, I am not familiar with clang-cl).  
  
4. Looking at the library's expected failures (libs/contract/meta/explicit-failures-markputs.xml), the public_function-virtual_access_multi test is known to fail on GCC 4.6 and 4.7 for the following reason, but I do not know if this failure on clang-cl is related to this:            
                "This test fails because this compiler seems to incorrectly check  
                access level of members in base classes in a context when only  
                derived class members are used.  
                This seems to be fixed in GCC 4.8 (possibly related to  
                https://gcc.gnu.org/bugzilla/show_bug.cgi?id=57973)."  
To check that, try to compile this sample program and send me the full out put of that: https://gcc.gnu.org/bugzilla/attachment.cgi?id=30547  
  
For the rest, I see this test (and all other tests) pass on all Boost's regression test compilers and platforms: https://www.boost.org/development/tests/master/developer/contract.html

---

## Comment 2

> Username: yuxianch  
> Created at: 2019-11-30 07:59:28 UTC  
> Url: https://github.com/boostorg/contract/issues/26#issuecomment-559923343  

1 & 2. Cause the output is quite large, I will send the output to your email.  
3. clang-cl version  
```  
clang version 10.0.0  
```  
4. the output when using clang-cl to run the sample  
```  
ksh-3.2$ clang-cl test_reproducer.cpp -o test_reproducer.exe  
test_reproducer.cpp(25,18): warning: address of 'this->D<int>::bptr' will always evaluate to 'true' [-Wpointer-bool-conversion]  
      return &D::bptr;  
      ~~~~~~  ~~~^~~~  
test_reproducer.cpp(37,6): note: in instantiation of member function 'D<int>::testB' requested here  
   d.testB(); // without this line g++ reports no error  
     ^  
1 warning generated.  
ksh-3.2$ ./test_reproducer.exe ; echo $?  
0  
```  
Thanks a lot!

---

## Comment 3

> Username: lcaminiti  
> Created at: 2020-03-04 15:25:42 UTC  
> Url: https://github.com/boostorg/contract/issues/26#issuecomment-594598853  

Unfortunately, I am not able to reproduce this issue. I do not have the specific compiler/platform that is giving this problem, but this test passes on all my compilers/platforms combinations, plus all those of Boost regressions tests https://www.boost.org/development/tests/develop/developer/contract.html and https://www.boost.org/development/tests/master/developer/contract.html. Therefore I am not able to investigate this issue more... it might be compiler/platform specific and I am closing this issue for now unless it is found also on other compilers/platforms.
