# #172 - Compilation issue [Closed]

> Username: adhayalan  
> Created at: 2023-09-04 07:42:49 UTC  
> Updated at: 2023-09-04 11:16:55 UTC  
> Closed at: 2023-09-04 11:16:54 UTC  
> Url: https://github.com/boostorg/nowide/issues/172  

- The C compiler identification is GNU 12.2.0  
-- The CXX compiler identification is GNU 12.2.0  
-- Detecting C compiler ABI info  
-- Detecting C compiler ABI info - done  
-- Check for working C compiler: /usr/bin/cc - skipped  
-- Detecting C compile features  
-- Detecting C compile features - done  
-- Detecting CXX compiler ABI info  
-- Detecting CXX compiler ABI info - done  
-- Check for working CXX compiler: /usr/bin/c++ - skipped  
-- Detecting CXX compile features  
-- Detecting CXX compile features - done  
-- Found Git: /usr/bin/git (found version "2.39.2")   
-- Detected Linker: /usr/bin/ld  
-- Setting build type to 'Debug' as none was specified.  
-- Performing Test HAS_CXX_FLAG__fstack_protector_all  
-- Performing Test HAS_CXX_FLAG__fstack_protector_all - Success  
-- Performing Test HAS_CXX_FLAG__Werror  
-- Performing Test HAS_CXX_FLAG__Werror - Success  
-- Performing Test HAS_CXX_FLAG__Wall  
-- Performing Test HAS_CXX_FLAG__Wall - Success  
-- Performing Test HAS_CXX_FLAG__Wextra  
-- Performing Test HAS_CXX_FLAG__Wextra - Success  
-- Performing Test HAS_CXX_FLAG__Wpointer_arith  
-- Performing Test HAS_CXX_FLAG__Wpointer_arith - Success  
-- Performing Test HAS_CXX_FLAG__Winvalid_pch  
-- Performing Test HAS_CXX_FLAG__Winvalid_pch - Success  
-- Performing Test HAS_CXX_FLAG__Wcast_align  
-- Performing Test HAS_CXX_FLAG__Wcast_align - Success  
-- Performing Test HAS_CXX_FLAG__Wwrite_strings  
-- Performing Test HAS_CXX_FLAG__Wwrite_strings - Success  
-- Performing Test HAS_CXX_FLAG__Woverloaded_virtual  
-- Performing Test HAS_CXX_FLAG__Woverloaded_virtual - Success  
-- Performing Test HAS_CXX_FLAG__Wnon_virtual_dtor  
-- Performing Test HAS_CXX_FLAG__Wnon_virtual_dtor - Success  
-- Performing Test HAS_CXX_FLAG__Wold_style_cast  
-- Performing Test HAS_CXX_FLAG__Wold_style_cast - Success  
-- Performing Test HAS_CXX_FLAG__Wstrict_null_sentinel  
-- Performing Test HAS_CXX_FLAG__Wstrict_null_sentinel - Success  
-- Performing Test HAS_CXX_FLAG__Wsign_promo  
-- Performing Test HAS_CXX_FLAG__Wsign_promo - Success  
-- Performing Test HAS_CXX_FLAG__fdiagnostics_show_option  
-- Performing Test HAS_CXX_FLAG__fdiagnostics_show_option - Success  
-- Performing Test HAS_CXX_FLAG__fvisibility_hidden_fvisibility_inlines_hidden  
-- Performing Test HAS_CXX_FLAG__fvisibility_hidden_fvisibility_inlines_hidden - Success  
-- Performing Test HAS_CXX_FLAG__O1_D_FORTIFY_SOURCE_2  
-- Performing Test HAS_CXX_FLAG__O1_D_FORTIFY_SOURCE_2 - Success  
-- Performing Test HAS_CXX_FLAG__O1_fsanitize_address_fno_omit_frame_pointer  
-- Performing Test HAS_CXX_FLAG__O1_fsanitize_address_fno_omit_frame_pointer - Success  
-- Performing Test HAS_CXX_FLAG__O1_fsanitize_thread  
-- Performing Test HAS_CXX_FLAG__O1_fsanitize_thread - Success  
-- Performing Test HAS_CXX_FLAG__fsanitize_memory_fsanitize_memory_track_origins_2_fPIE_fno_omit_frame_pointer  
-- Performing Test HAS_CXX_FLAG__fsanitize_memory_fsanitize_memory_track_origins_2_fPIE_fno_omit_frame_pointer - Failed  
-- Performing Test HAS_CXX_FLAG__fsanitize_undefined_fno_sanitize_recover_all_fno_omit_frame_pointer  
-- Performing Test HAS_CXX_FLAG__fsanitize_undefined_fno_sanitize_recover_all_fno_omit_frame_pointer - Success  
-- Performing Test HAS_CXX_FLAG__fprofile_arcs_ftest_coverage  
-- Performing Test HAS_CXX_FLAG__fprofile_arcs_ftest_coverage - Success  
-- Performing Test CMAKE_HAVE_LIBC_PTHREAD  
-- Performing Test CMAKE_HAVE_LIBC_PTHREAD - Success  
-- Found Threads: TRUE    
-- Performing Test BOOST_NOWIDE_HAS_LFS  
-- Performing Test BOOST_NOWIDE_HAS_LFS - Success  
-- Performing Test BOOST_NOWIDE_HAS_INIT_PRIORITY  
-- Performing Test BOOST_NOWIDE_HAS_INIT_PRIORITY - Success  
-- Found Boost: /usr/lib/x86_64-linux-gnu/cmake/Boost-1.74.0/BoostConfig.cmake (found suitable version "1.74.0", minimum required is "1.56")    
-- Performing Test HAS_CXX_FLAG__Wl__no_undefined  
-- Performing Test HAS_CXX_FLAG__Wl__no_undefined - Success  
Checking for CppMicroServices resource linking capability...yes  
-- Looking for C++ include cxxabi.h  
-- Looking for C++ include cxxabi.h - found  
Framework version is 3.7.6  
HttpService version is 0.1.0  
WebConsole version is 0.1.0  
-- Performing Test HAS_CXX_FLAG__Wno_switch  
-- Performing Test HAS_CXX_FLAG__Wno_switch - Success  
ShellService version is 0.1.0  
LogServiceImpl version is 1.0.0  
DeclarativeServices version is 1.5.3  
ConfigurationAdmin version is 1.3.4  
-- Found CppMicroServices: /devlop/CppMicroServices/build/CppMicroServicesConfig.cmake (found version "3.7.6") found components: ShellService   
-- Found Doxygen: /usr/bin/doxygen (found version "1.9.4") found components: doxygen dot   
-- Configuring done  
**CMake Error at tools/rc/CMakeLists.txt:23 (target_link_libraries):  
  Target "usResourceCompiler" links to:  
  
    nowide::nowide  
  
  but the target was not found.  Possible reasons include:  
  
    * There is a typo in the target name.  
    * A find_package call is missing for an IMPORTED target.  
    * An ALIAS target is missing.**

---

## Comment 1

> Username: Flamefire  
> Created at: 2023-09-04 08:27:59 UTC  
> Url: https://github.com/boostorg/nowide/issues/172#issuecomment-1704835247  

There is quite some information missing on how you use Nowide, so I need to guess:  
  
This is compiling a third_party software, not Boost.Nowide, isn't it?  
  
You are using Boost 1.74 which includes Boost.Nowide yet  
  
>  Performing Test BOOST_NOWIDE_HAS_LFS  
  
suggests you are including it in your build somehow.  
  
> Target "usResourceCompiler" links to:  
>   
> nowide::nowide  
  
The correct target is `Boost::nowide`. The `nowide::nowide` target is for the standalone version only which you seemingly don't use.  
  
So why not just use `find_package(Boost 1.73 COMPONENTS nowide)` instead of building it yourself? What is your intention?

---

## Comment 2

> Username: adhayalan  
> Created at: 2023-09-04 10:50:01 UTC  
> Url: https://github.com/boostorg/nowide/issues/172#issuecomment-1705048105  

I used the latest source from github. In which I do not see the CMakeLists.txt in thirdparty/boost/nowide ( cmake complained this error ). Hence I downloaded the nowide code from github and compiled it. While using the CppMicroservices cmake command, I got the above mentioned error.

---

## Comment 3

> Username: Flamefire  
> Created at: 2023-09-04 11:16:55 UTC  
> Url: https://github.com/boostorg/nowide/issues/172#issuecomment-1705088784  

> I used the latest source from github. In which I do not see the CMakeLists.txt in thirdparty/boost/nowide ( cmake complained this error ). Hence I downloaded the nowide code from github and compiled it. While using the CppMicroservices cmake command, I got the above mentioned error.  
  
Did you mean to report this to https://github.com/CppMicroServices/CppMicroServices?  
  
When I look into the [mentioned folder](https://github.com/CppMicroServices/CppMicroServices/tree/development/third_party/boost) I see they add nowide as a submodule but from the standalone branch. So either check out CppMicroServices recursively including submodules or download the standalone version/branch of nowide into the folder.
