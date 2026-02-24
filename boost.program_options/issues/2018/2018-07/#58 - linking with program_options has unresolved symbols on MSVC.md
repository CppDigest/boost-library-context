# #58 - linking with program_options has unresolved symbols on MSVC [Open]

> Username: emmenlau  
> Created at: 2018-07-07 11:55:58 UTC  
> Updated at: 2024-07-25 07:48:12 UTC  
> Url: https://github.com/boostorg/program_options/issues/58  

I'm developing a simple command line client application against boost::program_options. Everything works fine on Linux with gcc-4.8, gcc-5.3 and gcc-6.3, on Darwin with XCode 7 and on Windows with MinGW-w64. But on Windows with MSVC Build Tools x64 from 2015 and 2017 I get two unresolved symbols:  
  
```  
LightBISClientCMDLine.cc.obj : error LNK2001: unresolved external symbol "class std::basic_string<char,struct std::char_traits<char>,class std::allocator<char> > boost::program_options::arg" (?arg@program_options@boost@@3V?$basic_string@DU?$char_traits@D@std@@V?$allocator@D@2@@std@@A)  
LightBISClientCMDLine.cc.obj : error LNK2001: unresolved external symbol "public: static unsigned int const boost::program_options::options_description::m_default_line_length" (?m_default_line_length@options_description@program_options@boost@@2IB)  
```  
  
I checked program_options.dll and I am under the impression that those two symbols are defined there. I get exact matches for `?arg@program_options@boost@@3V?$basic_string@DU?$char_traits@D@std@@V?$allocator@D@2@@std@@A` and ?m_default_line_length@options_description@program_options@boost@@2IB.` Also all other symbols from program_options are resolved correctly. Here is the details build log with a verbose linker message: ​http://data.biodataanalysis.de/tmp/boost_program_options_linker_error_emmenlau.txt  
  
I checked the build log to see that the library was built with `-DBOOST_PROGRAM_OPTIONS_DYN_LINK=1`.  
  
As far as I could see, it's not supported to export STL classes like `std::string` from a DLL with Visual Studio 2015 and 2017 because it seems considered bad practice. There are various reports that indicate that exporting STL classes can cause issues when mixing different runtimes or compiler versions. Is it possible that Microsoft just disabled that option alltogether?  
  
I found related reports here:  
  * ​https://social.msdn.microsoft.com/Forums/vstudio/en-US/df99b712-c00b-4af0-82fd-3764c8b6cbec/exporting-stdstring-from-a-dll-does-not-export-stdstringnpos  
  * ​https://stackoverflow.com/a/13866797/7200132  
  
Here is an example that shows the error. The build is configured with cmake: https://github.com/emmenlau/BDAMinimalTestCase

---

## Comment 1

> Username: emmenlau  
> Created at: 2018-07-07 13:28:16 UTC  
> Url: https://github.com/boostorg/program_options/issues/58#issuecomment-403215664  

I checked the code and would appreciate some feedback. The first issue is about the following source https://github.com/boostorg/program_options/blob/d95d31684832075fda04c9fc916f8ec875552763/include/boost/program_options/detail/value_semantic.hpp#L22  
  
The method/section in question is:  
```  
    extern BOOST_PROGRAM_OPTIONS_DECL std::string arg;  
      
    template<class T, class charT>  
    std::string  
    typed_value<T, charT>::name() const  
    {  
        std::string const& var = (m_value_name.empty() ? arg : m_value_name);  
        if (!m_implicit_value.empty() && !m_implicit_value_as_text.empty()) {  
            std::string msg = "[=" + var + "(=" + m_implicit_value_as_text + ")]";  
            if (!m_default_value.empty() && !m_default_value_as_text.empty())  
                msg += " (=" + m_default_value_as_text + ")";  
            return msg;  
        }  
        else if (!m_default_value.empty() && !m_default_value_as_text.empty()) {  
            return var + " (=" + m_default_value_as_text + ")";  
        } else {  
            return var;  
        }  
    }  
```  
AFAICS `arg` is used only in this one place. In my understanding is is a dummy, in case `std::string m_value_name` is empty. However I do not understand why `arg` is required in the first place? Since `arg` is never set, it will also be an empty string. So why prefer `arg` over `m_value_name` when the latter is empty? Could the code be simplified to:  
  
```  
    template<class T, class charT>  
    std::string  
    typed_value<T, charT>::name() const  
    {  
        std::string const& var = m_value_name;  
        if (!m_implicit_value.empty() && !m_implicit_value_as_text.empty()) {  
            std::string msg = "[=" + var + "(=" + m_implicit_value_as_text + ")]";  
            if (!m_default_value.empty() && !m_default_value_as_text.empty())  
                msg += " (=" + m_default_value_as_text + ")";  
            return msg;  
        }  
        else if (!m_default_value.empty() && !m_default_value_as_text.empty()) {  
            return var + " (=" + m_default_value_as_text + ")";  
        } else {  
            return var;  
        }  
    }  
```

---

## Comment 2

> Username: vprus  
> Created at: 2018-07-07 20:24:27 UTC  
> Url: https://github.com/boostorg/program_options/issues/58#issuecomment-403240874  

Your project appears to set `BOOST_DYN_LINK=1`. I don't think that's right spelling, it should be `BOOST_ALL_DYN_LINK=1'. Does that change anything? You might want to reconfigure your project, and build with verbose output, to make sure that all sources are built with BOOST_ALL_DYN_LINK=1.

---

## Comment 3

> Username: emmenlau  
> Created at: 2018-07-11 09:21:44 UTC  
> Url: https://github.com/boostorg/program_options/issues/58#issuecomment-404103581  

Dear @vprus , thanks for the quick response, and thanks for spotting this mistake. But I checked the build log to see that the library was built with `-DBOOST_PROGRAM_OPTIONS_DYN_LINK=1`. Would that not be sufficient?  
  
And more importantly, can you help understand if the current implementation of `extern BOOST_PROGRAM_OPTIONS_DECL std::string arg;` is useful? It seems to cause problems at least in some scenarios (like mine now). Could it be replaced with getter / setter methods like `boost::program_options::set_default_arg(const std::string&)`?

---

## Comment 4

> Username: vprus  
> Created at: 2018-07-13 19:18:32 UTC  
> Url: https://github.com/boostorg/program_options/issues/58#issuecomment-404928460  

Mario,  
  
dynamic library is a two-sided story. First, the library must be built with BOOST_PROGRAM_OPTIONS_DYN_LINK=1. Second, when using, BOOST_ALL_DYN_LINK=1, or BOOST_PROGRAM_OPTIONS_DYN_LINK=1 must be set. That causes various symbols to be declared with __declspec(dllexport). When it's not done when using the library, you get performance penalty for some functions and errors for variables.  
  
I agree with your analysis for the code, it seems that args variable could be removed. It's there for at least 14 years, so the original motivation is hard to recover. At the same time, it would hide the wrong compilation options. Instead of an error, you would get less efficient operation of the code.

---

## Comment 5

> Username: vinniefalco  
> Created at: 2019-01-22 16:30:53 UTC  
> Url: https://github.com/boostorg/program_options/issues/58#issuecomment-456465784  

This problem still exists as of 1.69 and later. I tried linking my program on MSVC against program_options and got the same two errors. The resolution is to link statically, so it is possible this is something to do with dllexport on those data members.

---

## Comment 6

> Username: monsdar  
> Created at: 2019-07-15 19:02:52 UTC  
> Updated at: 2019-07-15 19:06:29 UTC  
> Url: https://github.com/boostorg/program_options/issues/58#issuecomment-511528899  

I'm having similar issues when using the [Conan package for Boost 1.69.0](https://bintray.com/conan-community/conan/boost%3Aconan/1.69.0%3Astable)  
* from here: https://github.com/conan-community/conan-boost/blob/release/1.69.0/conanfile.py  
* with `shared=True`  
* with `header_only=False`  
  
Even though my application compiles with `BOOST_ALL_DYN_LINK` defined, I get the above mentioned linking issues. It worked fine with Boost 1.59, though I can't be sure how exactly te libs were built back then. (UPDATE: I just checked, the 1.59 recipe that was used did define BOOST_ALL_DYN_LINK in the same way it was defined with the 1.69 version of the recipe. No idea why the resulting lib did not cause any errors)  
  
I cannot see `BOOST_ALL_DYN_LINK=1` being declared within the build-method of the Conan recipe, but only later during package_info (for all consuming packages). Is this what's potentially missing?

---

## Comment 7

> Username: grisumbras  
> Created at: 2019-07-15 19:39:38 UTC  
> Url: https://github.com/boostorg/program_options/issues/58#issuecomment-511540747  

I checked the Conan recipe. It sets `link=shared` during build, which should trigger setting define `BOOST_PROGRAM_OPTIONS_DYN_LINK=1` for `boost_program_options` target. Can you check if the define is indeed set during the build?

---

## Comment 8

> Username: monsdar  
> Created at: 2019-07-15 19:55:53 UTC  
> Url: https://github.com/boostorg/program_options/issues/58#issuecomment-511545996  

checking the same right now... I get the following build flags:  
```  
BuildFlag: target-os=windows  
BuildFlag: architecture=x86  
BuildFlag: address-model=64  
BuildFlag: binary-format=pe  
BuildFlag: abi=ms  
BuildFlag: runtime-link=shared  
BuildFlag: threading=multi  
BuildFlag: toolset=msvc  
BuildFlag: link=shared  
BuildFlag: variant=debug  
BuildFlag: --without-python  
```  
  
When I additionally define `cxxflags="-DBOOST_ALL_DYN_LINK=1"` I'm getting errors though. Seems that somehow `DBOOST_ALL_NO_LIB` and `DBOOST_All_STATIC_LINK` is activated as well... Looking further into this right now.

---

## Comment 9

> Username: monsdar  
> Created at: 2019-07-15 20:07:13 UTC  
> Updated at: 2019-07-15 20:07:33 UTC  
> Url: https://github.com/boostorg/program_options/issues/58#issuecomment-511549788  

Ok, seems the above was a bit mixed up due to my changes when trying out different things. With the original recipe the buildflags are the same as above. For program_options in particular it states `-DBOOST_ALL_NO_LIB=1` along with `-DBOOST_PROGRAM_OPTIONS_DYN_LINK=1`.

---

## Comment 10

> Username: emmenlau  
> Created at: 2019-07-16 07:41:00 UTC  
> Url: https://github.com/boostorg/program_options/issues/58#issuecomment-511703843  

@vprus would you be open to change this implementation detail into something more robust using getters and setters? If the getters use const reference, their efficiency should be comparable to the current implementation. And the setters can't avoid one string assignment anyways, so it should not make much of a difference performance-wise.  
  
The main downside is that this will break the API. But I think the increased robustness of the code makes up for that?

---

## Comment 11

> Username: monsdar  
> Created at: 2019-07-16 09:22:00 UTC  
> Url: https://github.com/boostorg/program_options/issues/58#issuecomment-511738083  

In my case I found it to be on the consumers end... Somehow in my consumers CMakeFiles.txt `Boost_LIBRARIES` was never filled. Using `Boost_PROGRAM_OPTIONS_LIBRARY` to link against worked though. Not sure what's behind this, still investigating. Sorry for the noise in this issue :/

---

## Comment 12

> Username: emmenlau  
> Created at: 2019-07-16 09:26:41 UTC  
> Url: https://github.com/boostorg/program_options/issues/58#issuecomment-511739790  

Personally I found that a recent cmake in combination with newest boost 1.70.0 changed the behavior of `Boost_LIBRARIES`, the variable is never filled for me. The best solution I could find is to switch from `$Boost_LIBRARIES` to `Boost::program_options`. With this explicit naming, there is no need to set the include directory for boost anymore, cmake figures the dependency from the link command.

---

## Comment 13

> Username: grisumbras  
> Created at: 2019-07-16 09:43:02 UTC  
> Url: https://github.com/boostorg/program_options/issues/58#issuecomment-511745358  

From CMake 3.15 docs for FindBoost module:  
>If Boost was built using the boost-cmake project or from Boost 1.70.0 on it provides a package configuration file for use with find_package’s config mode. This module looks for the package configuration file called BoostConfig.cmake or boost-config.cmake and stores the result in CACHE entry “Boost_DIR”. If found, the package configuration file is loaded and this module returns with no further action. See documentation of the Boost CMake package configuration for details on what it provides.  
>  
> Set Boost_NO_BOOST_CMAKE to ON, to disable the search for boost-cmake.  
  
Since version 1.70 Boost provides its own CMake configuration that is based on targets (as it is the proper way).

---

## Comment 14

> Username: vprus  
> Created at: 2019-07-16 09:45:04 UTC  
> Url: https://github.com/boostorg/program_options/issues/58#issuecomment-511746032  

@emmenlau: I might have lost track of the discussion, as it mentions cmake and something called conan. May I suggest we backtrack and establish whether using the documented way to linking to program_options works or not? Specifically, when using DLL, one must set `BOOST_ALL_DYN_LINK=1`, or `BOOST_PROGRAM_OPTIONS_DYN_LINK=1`. Is there a reproducible example (preferrably using cl.exe directly, not any additional tools) where these instructions fail to link?

---

## Comment 15

> Username: vprus  
> Created at: 2019-07-16 09:52:05 UTC  
> Url: https://github.com/boostorg/program_options/issues/58#issuecomment-511748556  

When these defines are not properly set, the behaviour for functions is as documented at https://devblogs.microsoft.com/oldnewthing/20060726-00/?p=30363. For reference, see also https://devblogs.microsoft.com/oldnewthing/20060724-00/?p=30403 and https://devblogs.microsoft.com/oldnewthing/20060721-06/?p=30433. In other words:  
- For functions, linker can limp along, but it has some performance downsides  
- For variables, you get the error from the first message  
Turning the variable into function, as you suggest, would get rid of the error message, but it will still the use slightly inefficient calling method. While one can argue it does not matter for options parsing, if one has defines wrong for program_option, the defines are likely wrong for all other compiled Boost libraries what might be more performance critical, so probably failing fast is better that silently doing wrong thing. Anyway, that's a discussion we can have after determining whether the problem is indeed wrong defines, and not some new issue.

---

## Comment 16

> Username: emmenlau  
> Created at: 2019-07-16 10:57:26 UTC  
> Url: https://github.com/boostorg/program_options/issues/58#issuecomment-511768589  

Dear @vprus, I agree that the problem cases all seem to originate from a false use of the compiler defines. So I do not want to judge the correctness of the current implementation.  
  
However from a users perspective, I personally would much prefer a more robust implementation that does not depend on defines. Especially in this case, it seems program_options could completely alleviate the need for the compiler defines _if_ two symbols would not be exported directly but changed to getters/setters.  
  
Would the benefit of increased robustness outweigh the effort of changing the code and tests?

---

## Comment 17

> Username: vprus  
> Created at: 2019-07-16 11:22:41 UTC  
> Url: https://github.com/boostorg/program_options/issues/58#issuecomment-511775673  

As I explain above, I don't think it would alleviate the need for the compiler defines. It would mean that wrong/missing defines silently result in suboptimal function calls, as opposed to up-front error. Not sure that silent performance issues can be called robust. We certainly can discuss if an extra indirection for function calls is minor, but please, can we first determine if correct options work? If there's some other issue, then we'd spent time discussing and possibly implementing a fix that would not help.  
  
So, does anybody in this thread willing to verify whether correct options fix the issue?

---

## Comment 18

> Username: emmenlau  
> Created at: 2019-07-16 13:09:35 UTC  
> Url: https://github.com/boostorg/program_options/issues/58#issuecomment-511808569  

Dear @vprus, according to my tracking of issue reports, nobody seems to have a reproducible example where your instructions (for correct compiler options) fail to link. Therefore I'm under the impression that the correct options **do** fix the issue.  
  
In short, I'm under the impression that there is no **bug** as long as the compiler options are correct.

---

## Comment 19

> Username: emmenlau  
> Created at: 2019-07-16 13:14:54 UTC  
> Updated at: 2022-07-28 18:32:44 UTC  
> Url: https://github.com/boostorg/program_options/issues/58#issuecomment-511810523  

But, even if there is no bug, I think the behavior could be improved. The current behavior is:  
 * if compiler options are set correctly, the build works and the code is efficient on all platforms  
 * if compiler options are not set correctly, the build fails on on MSVC  
  
I would prefer another behavior:  
 * if compiler options are set correctly, the build works and the code is efficient on all platforms  
 * if compiler options are *not* set correctly, the build works but the code is slightly less efficient on MSVC  
  
This would help novice users or people with complex setups to get a build working quickly. And it does not block power users from getting a good and efficient build.  
  
But really, that is just my personal preference.

---

## Comment 20

> Username: vprus  
> Created at: 2019-07-16 13:39:12 UTC  
> Url: https://github.com/boostorg/program_options/issues/58#issuecomment-511819881  

@emmenlau: that's for clarifying your point of view. I'll think about this a few days - feel free to ping if I don't respond one way or another by next Monday.

---

## Comment 21

> Username: emmenlau  
> Created at: 2022-07-28 18:33:21 UTC  
> Url: https://github.com/boostorg/program_options/issues/58#issuecomment-1198499950  

Hey, just a small ping on this rather dated issue. Should we change anything in the implementation or close the issue?

---

## Comment 22

> Username: looooo  
> Created at: 2024-07-24 17:51:58 UTC  
> Url: https://github.com/boostorg/program_options/issues/58#issuecomment-2248594614  

I am running into the same issue here: https://github.com/conda-forge/freecad-feedstock/pull/122  
What was the solution for this issue?

---

## Comment 23

> Username: emmenlau  
> Created at: 2024-07-24 18:02:06 UTC  
> Url: https://github.com/boostorg/program_options/issues/58#issuecomment-2248611454  

> I am running into the same issue here: [conda-forge/freecad-feedstock#122](https://github.com/conda-forge/freecad-feedstock/pull/122) What was the solution for this issue?  
  
You can alleviate the problem with a correct combination of compiler defines. Specifically, when using DLL, one must set `BOOST_ALL_DYN_LINK=1`, or `BOOST_PROGRAM_OPTIONS_DYN_LINK=1`. Is that what you do?

---

## Comment 24

> Username: looooo  
> Created at: 2024-07-25 07:02:34 UTC  
> Url: https://github.com/boostorg/program_options/issues/58#issuecomment-2249602831  

set "CXXFLAGS= -DBOOST_PROGRAM_OPTIONS_DYN_LINK=1" solved my issue, thanks.

---

## Comment 25

> Username: looooo  
> Created at: 2024-07-25 07:10:57 UTC  
> Url: https://github.com/boostorg/program_options/issues/58#issuecomment-2249615979  

osx-arm has several issues:  
```  
ImportError: cannot import name '_coin' from partially initialized module 'pivy' (most likely due to a circular import) (/Users/runner/miniforge3/conda-bld/freecad_1721851139143/_build_env/venv/lib/python3.12/site-packages/pivy/__init__.py)  
```  
```  
2024-07-24T20:06:26.6598200Z ModuleNotFoundError: No module named 'shiboken6.Shiboken'  
2024-07-24T20:06:26.6675330Z CMake Warning at cMake/FreeCAD_Helpers/SetupShibokenAndPyside.cmake:133 (message):  
2024-07-24T20:06:26.6675980Z   ==================================  
2024-07-24T20:06:26.6676190Z   
2024-07-24T20:06:26.6676650Z   Shiboken6 Python module not found.  
2024-07-24T20:06:26.6676910Z   
2024-07-24T20:06:26.6677660Z   ==================================  
```  
  
```  
2024-07-24T20:06:26.7595490Z     from shiboken6.Shiboken import *  
2024-07-24T20:06:26.7596170Z ModuleNotFoundError: No module named 'shiboken6.Shiboken'  
```  
  
```  
2024-07-24T20:06:26.7647660Z   ================================  
2024-07-24T20:06:26.7647850Z   
2024-07-24T20:06:26.7648070Z   PySide6 Python module not found.  
2024-07-24T20:06:26.7648300Z   
2024-07-24T20:06:26.7648580Z   ================================  
```  
```  
fatal: not a git repository (or any of the parent directories): .git  
```  
  
I guess some of them are cross-compiling issues.

---

## Comment 26

> Username: vprus  
> Created at: 2024-07-25 07:48:11 UTC  
> Url: https://github.com/boostorg/program_options/issues/58#issuecomment-2249679767  

Shiboken6 is something to do with Qt and Python? I doubt this is related to program_options
