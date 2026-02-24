# #124 - Test operations_test.cpp failed for not finding specified path in clang on Windows [Closed]

> Username: yuxianch  
> Created at: 2019-11-20 07:03:27 UTC  
> Updated at: 2019-11-23 10:40:13 UTC  
> Closed at: 2019-11-23 10:36:12 UTC  
> Url: https://github.com/boostorg/filesystem/issues/124  

Hi,  
I found test [operations_test.cpp](https://github.com/boostorg/filesystem/tree/develop/test/operations_test.cpp) failed for not finding specified path in clang on Windows when running function temp_directory_path_tests().  
Below is the compiling and linking commands.  
```  
clang "..\..\..\libs\filesystem\src\utf8_codecvt_facet.cpp" -Foutf8_codecvt_facet.obj -std=c++17 -D_CRT_USE_BUILTIN_OFFSETOF -c -DBOOST_ALL_NO_LIB=1 -DBOOST_FILESYSTEM_DYN_LINK=1 -I"..\..\.."  
clang "..\..\..\libs\filesystem\src\codecvt_error_category.cpp" -Focodecvt_error_category.obj -std=c++17 -D_CRT_USE_BUILTIN_OFFSETOF -c -DBOOST_ALL_NO_LIB=1 -DBOOST_FILESYSTEM_DYN_LINK=1 -I"..\..\.."  
clang "..\..\..\libs\filesystem\src\portability.cpp" -Foportability.obj -std=c++17 -D_CRT_USE_BUILTIN_OFFSETOF -c -DBOOST_ALL_NO_LIB=1 -DBOOST_FILESYSTEM_DYN_LINK=1 -I"..\..\.."  
clang "..\..\..\libs\filesystem\src\path_traits.cpp" -Fopath_traits.obj -std=c++17 -D_CRT_USE_BUILTIN_OFFSETOF -c -DBOOST_ALL_NO_LIB=1 -DBOOST_FILESYSTEM_DYN_LINK=1 -I"..\..\.."  
clang "..\..\..\libs\filesystem\src\windows_file_codecvt.cpp" -Fowindows_file_codecvt.obj -std=c++17 -D_CRT_USE_BUILTIN_OFFSETOF -c -DBOOST_ALL_NO_LIB=1 -DBOOST_FILESYSTEM_DYN_LINK=1 -I"..\..\.."  
clang "..\..\..\libs\filesystem\src\unique_path.cpp" -Founique_path.obj -std=c++17 -D_CRT_USE_BUILTIN_OFFSETOF -c -DBOOST_ALL_NO_LIB=1 -DBOOST_FILESYSTEM_DYN_LINK=1 -I"..\..\.."  
clang "..\..\..\libs\filesystem\src\path.cpp" -Fopath.obj -std=c++17 -D_CRT_USE_BUILTIN_OFFSETOF -c -DBOOST_ALL_NO_LIB=1 -DBOOST_FILESYSTEM_DYN_LINK=1 -I"..\..\.."  
clang "..\..\..\libs\filesystem\src\operations.cpp" -Fooperations.obj -std=c++17 -D_CRT_USE_BUILTIN_OFFSETOF -c -DBOOST_ALL_NO_LIB=1 -DBOOST_FILESYSTEM_DYN_LINK=1 -I"..\..\.."  
clang "operations_test.cpp" -Fooperations_test.obj -std=c++17 -D_CRT_USE_BUILTIN_OFFSETOF -c -DBOOST_ALL_NO_LIB=1 -DBOOST_FILESYSTEM_DYN_LINK=1 -I"..\..\.."  
link /nologo /DEBUG /subsystem:console /out:"operations_test_clang.exe" operations_test.obj utf8_codecvt_facet.obj codecvt_error_category.obj portability.obj path_traits.obj windows_file_codecvt.obj unique_path.obj path.obj operations.obj  
```  
I got the following output when runing the executable file.  
```  
ksh-3.2$ ./operations_test_clang.exe  
BOOST_WINDOWS_API is defined  
...  
temp_directory_path_tests...  
 temp_directory_path() is "C:/Users/yuxianch/AppData/Local/Temp/3"  
  
ERROR  ERROR  ERROR  ERROR  ERROR  ERROR  ERROR  ERROR  ERROR  ERROR  ERROR  
  
****************************** std::exception *****************************  
boost::filesystem::create_directory: The system cannot find the path specified: "op-test-e619-eefa\123456789012345678901234567890123456789012345678901234567890123456789012345678901234567890123456789012345678901234567890123456789012345678901234567890#"  
***************************************************************************  
```  
Can someone explain why this test failed in running temp_directory_path_tests()?  
Thanks!

---

## Comment 1

> Username: Lastique  
> Created at: 2019-11-20 08:23:28 UTC  
> Url: https://github.com/boostorg/filesystem/issues/124#issuecomment-555893816  

As you can see from the test code, it attempts to create a directory with long name in the test directory tree. For some reason, that test tree doesn't exist. The test tree is created in https://github.com/boostorg/filesystem/blob/93f0c76869c8a7a04b79f0719393f31b878284a2/test/operations_test.cpp#L2262.  
  
I suspect either someone intervened with the test and removed the test tree directory or there were other (prior) failures which manifested as the tree removal.

---

## Comment 2

> Username: yuxianch  
> Created at: 2019-11-23 10:40:13 UTC  
> Url: https://github.com/boostorg/filesystem/issues/124#issuecomment-557786767  

Thank you @Lastique . I will continue to investigate.
