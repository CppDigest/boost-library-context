# #25 - Smart functionality tests fail on clang-win [Closed]

> Username: apolukhin  
> Created at: 2018-12-09 06:33:40 UTC  
> Updated at: 2019-01-06 17:13:45 UTC  
> Closed at: 2019-01-06 17:13:45 UTC  
> Url: https://github.com/boostorg/dll/issues/25  

See https://ci.appveyor.com/project/apolukhin/boost-dll/builds/20870450/job/4044pxejtggcqrun  
  
Especially see   
```  
testing.capture-output ..\..\..\bin.v2\libs\dll\test\cpp_mangle_test.test\clang-win-7.0.0\debug\address-model-64\cxxstd-17-iso\threading-multi\cpp_mangle_test.run  
====== BEGIN OUTPUT ======  
Library: "..\..\..\bin.v2\libs\dll\test\clang-win-7.0.0\debug\address-model-64\cxxstd-17-iso\threading-multi\cpp_plugin-clangw7-mt-d-x64-1_69.dll"  
Symbols:   
??0some_class@some_space@@QEAA@$$QEAU01@@Z  
??0some_class@some_space@@QEAA@H@Z  
??0some_class@some_space@@QEAA@XZ  
??0some_father@some_space@@QEAA@XZ  
??1some_class@some_space@@UEAA@XZ  
??1some_father@some_space@@QEAA@XZ  
??4some_class@some_space@@QEAAAEAU01@$$QEAU01@@Z  
??4some_father@some_space@@QEAAAEAU01@AEBU01@@Z  
??_7some_class@some_space@@6B@  
?father_value@some_space@@3HA  
?func@some_class@some_space@@QECAHHH@Z  
?func@some_class@some_space@@QEDANNN@Z  
?func@some_class@some_space@@UEAAHHH@Z  
?func@some_class@some_space@@UEAANNN@Z  
?get@some_class@some_space@@QEBAHXZ  
?overloaded@@YAXH@Z  
?overloaded@@YAXN@Z  
?scoped_fun@some_space@@YAAEBHXZ  
?set@some_class@some_space@@QEAAXH@Z  
?set_value@some_class@some_space@@SAXAEBH@Z  
?size_of_some_class@some_space@@3_KA  
?unscoped_c_var@@3NB  
?unscoped_var@@3HA  
?use_variant@@YAXAEAV?$variant@HN@boost@@@Z  
?use_variant@@YAXAEAV?$variant@NH@boost@@@Z  
?value@some_class@some_space@@2HA  
?variable@some_space@@3NA  
this_  
cpp_mangle_test.cpp(48): test '!v.empty()' failed in function 'int main(int, char **)'  
cpp_mangle_test.cpp(57): test '!v.empty()' failed in function 'int main(int, char **)'  
cpp_mangle_test.cpp(61): test '!v.empty()' failed in function 'int main(int, char **)'  
cpp_mangle_test.cpp(66): test '!v.empty()' failed in function 'int main(int, char **)'  
cpp_mangle_test.cpp(72): test '!v1.empty()' failed in function 'int main(int, char **)'  
cpp_mangle_test.cpp(73): test '!v2.empty()' failed in function 'int main(int, char **)'  
cpp_mangle_test.cpp(74): test 'v1 != v2' failed in function 'int main(int, char **)'  
cpp_mangle_test.cpp(77): test '!v.empty()' failed in function 'int main(int, char **)'  
cpp_mangle_test.cpp(82): test '!v.empty()' failed in function 'int main(int, char **)'  
cpp_mangle_test.cpp(90): test '!ctor1.empty()' failed in function 'int main(int, char **)'  
cpp_mangle_test.cpp(93): test '!ctor2.empty()' failed in function 'int main(int, char **)'  
cpp_mangle_test.cpp(97): test '!v.empty()' failed in function 'int main(int, char **)'  
cpp_mangle_test.cpp(100): test '!v.empty()' failed in function 'int main(int, char **)'  
cpp_mangle_test.cpp(105): test '!dtor.empty()' failed in function 'int main(int, char **)'  
cpp_mangle_test.cpp(110): test '!var1.empty()' failed in function 'int main(int, char **)'  
cpp_mangle_test.cpp(111): test '!var2.empty()' failed in function 'int main(int, char **)'  
16 errors detected.  
   
EXIT STATUS: 1   
====== END OUTPUT ======  
```

---

## Comment 1

> Username: apolukhin  
> Created at: 2018-12-09 06:35:25 UTC  
> Url: https://github.com/boostorg/dll/issues/25#issuecomment-445514837  

@klemens-morgenstern could you please take a look at the issue?

---

## Comment 2

> Username: klemens-morgenstern  
> Created at: 2018-12-10 07:52:08 UTC  
> Url: https://github.com/boostorg/dll/issues/25#issuecomment-445722484  

Clang-win seems to use the MSVC abi, so the headers select the wrong one - fun stuff.  
  
Do you want the PR against this or your own Repo?
