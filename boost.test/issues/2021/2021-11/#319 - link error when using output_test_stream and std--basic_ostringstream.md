# #319 - link error when using output_test_stream and std::basic_ostringstream [Open]

> Username: markstijnman-shell  
> Created at: 2021-11-27 01:56:59 UTC  
> Updated at: 2022-02-27 01:45:37 UTC  
> Url: https://github.com/boostorg/test/issues/319  

When trying to use boost::output_test_stream while using std::ostring elsewhere, I get a link error saying that the destructor of std::basic_ostringstream is already defined in the boost unit_test_framework DLL (using dynamic linking, which is the default in vcpkg).  
  
I have attached a minimal example that shows the problem. It is a simple static library (lib.hpp and lib.cpp) that defines a function using std::ostringstream, and a test file (test.cpp) that uses this function in a test, and includes boost/test/tools/output_test_stream. The example uses CMake to set up the project.  
[src.zip](https://github.com/boostorg/test/files/7611013/src.zip)  
  
When compiling this project, I get the following error:  
`2>boost_unit_test_framework-vc140-mt.lib(boost_unit_test_framework-vc142-mt-x64-1_75.dll) : error LNK2005: "public: void __cdecl std::basic_ostringstream<char,struct std::char_traits<char>,class std::allocator<char> >::`vbase destructor'(void)" (??_D?$basic_ostringstream@DU?$char_traits@D@std@@V?$allocator@D@2@@std@@QEAAXXZ) already defined in test_lib.lib(lib.obj)`  
  
Simply removing the include file for output_test_stream removes the error. Removing std::ostringstream from lib.cpp also removes the  link error. Obviously neither are acceptable workarounds in a real project.   
  
I believe this issue is the same as the one reported in [this StackOverflow post](https://stackoverflow.com/questions/37922117/stdbasic-ostringstream-exported-by-boost-unit-test-framework-dll-results-in-an), which was also reported to the [boost mailing list here](https://lists.boost.org/boost-users/2016/06/86332.php). Hopefully by reporting it here with a minimal example, it can now be addressed, as it should now be easily seen that this isn't a matter of mixing runtimes.
