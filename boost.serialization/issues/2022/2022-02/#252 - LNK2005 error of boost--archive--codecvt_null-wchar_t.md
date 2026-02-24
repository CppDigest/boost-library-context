# #252 - LNK2005 error of boost::archive::codecvt_null<wchar_t> [Closed]

> Username: AdmiralPellaeon  
> Created at: 2022-02-11 07:03:46 UTC  
> Updated at: 2022-02-24 21:07:59 UTC  
> Closed at: 2022-02-24 21:07:59 UTC  
> Url: https://github.com/boostorg/serialization/issues/252  

Hi,  
  
after switiching a x64 project from boost 1_73_0 to 1_78_0 and from VS 2019 to VS 2022, I get the linker error LNK2005: LNK2005	"public: __cdecl boost::archive::codecvt_null<wchar_t>::codecvt_null<wchar_t>(unsigned __int64)"(??0?$codecvt_null@_W@archive@boost@@QEAA@_K@Z) ist bereits in ARViewer_Tools.lib(ARViewer_Tools.dll) definiert.	Scenario	C:\Projekte\ARViewerNG\Source\Scenario\libboost_serialization-vc143-mt-x64-1_78.lib(codecvt_null.obj)  
  
The project consists of several parts. Regarding the problem I have a **DLL project** which uses boost::serialization. The project exports classes with the dllexport mechanism of the VS compiler. And this DLL is then **used in another project**, where boost::serialization is also used and linked. Import symbols are defined by the lib file from the DLL project, which is provided by the compiler.  
  
With boost 1_73_0 there was no problem at all. Now, I get the linker error. Does bs::serialization export symbols automatically when used in a VS DLL project? Or what could be the reason for this behavior and how can I fix this?  
  
Best regards

---

## Comment 1

> Username: robertramey  
> Created at: 2022-02-11 16:53:42 UTC  
> Url: https://github.com/boostorg/serialization/issues/252#issuecomment-1036411632  

It's hard for me to know what to do here.  
  
Smells to me that we're mixing pre-compiled code from one version and recently compiled code from a more recent version.  Runtime code is supposed to be backward compatible, but ... you know.  I don't know where you get your compiled version of boost libraries.  So it's hard for me to know.  In any case, I generally don't trust imported compiled code.  What I do is recompile boost from scratch on my local machine - and (re)run the test suite on my local machine.  Then build my apps - and almost never have these kinds of problems.  FYI, I get few reports of these problems either.  I know that's not much consolation, but I don't know what else to say.

---

## Comment 2

> Username: AdmiralPellaeon  
> Created at: 2022-02-14 07:33:49 UTC  
> Updated at: 2022-02-14 09:53:48 UTC  
> Url: https://github.com/boostorg/serialization/issues/252#issuecomment-1038749569  

Hi,  
  
I didn't mix libraries from different compiler version. I recompiled all 3rd party libraries which are used and linked in my program. So, I also recompiled boost. And as I wrote: I didn't change anything in the code, only recompiling. And it worked very well except 2 problems within boost. One is the above mentioned.  
  
When looking into the code, I saw that boost::archive::codecvt_null uses the define _**BOOST_SYMBOL_EXPORT**_. I suppose, that it is defined as dllexport resulting in an entry in the lib file. Subsequent, it results in multiple defined symbols in the project, which links against the DLL. But I do not use this symbol in my code. I never change it. So it must be set within boost I suppose. Perhaps it is a side effect of another boost library? Or was there a change in boos asio in the last 2, 3 releases regarding this symbol?

---

## Comment 3

> Username: AdmiralPellaeon  
> Created at: 2022-02-15 13:26:22 UTC  
> Url: https://github.com/boostorg/serialization/issues/252#issuecomment-1040271507  

I found this issue: [Issue 232](https://github.com/boostorg/serialization/issues/232)  
  
Sounds like my own problem. I tested the hint and changed codecvt_null.hpp and codecvt_null.cpp. But unfortunately, it doesn't work. With this revert change, I get another linker error in the first place: LNK2019 for public: class boost::archive::library_version_type __cdecl boost::archive::detail::basic_iarchive::get_library_version(void)const.  
  
I compared the codecvt_null.hpp in 1_73 and 1_78. In 1_73 there is no BOOST_SYMBOL_EXPORT, but it is in 1_78. Atm I suppose a side effect with this export mechanism which is new in this class.

---

## Comment 4

> Username: robertramey  
> Created at: 2022-02-16 17:51:33 UTC  
> Url: https://github.com/boostorg/serialization/issues/252#issuecomment-1041932074  

I've built and run all the tests on my local machine.  Only one case case failed and its not a linking problem.  Note this is for a multitude of compilers, compiler versions, settings for static and shared linkings, etc.  I also checked the [test matrix](https://www.boost.org/development/tests/develop/developer/serialization.html build uses b2 -dumptests as it's driver. A couple of observations:  
a)  I use the switch setting visibility-hidden in the b2 command line on all the tests. So the visibility macros are enabled everywhere.  This could be a problem when using at DLL compiled with different switches than the application.    
c) I run all tests with both static and shared settings.  I run on Xcode / clang so I'm not really sure what this means as it seems that Xcode ships with a shared library only so It's not cleat to me what happens when I build with tests with static linking to ....?  
d) I build/test with various versions gcc and clang.  
e) I don't have a windows computer/compiler and can't really justify the cost in time and money to set one up for this.  I do watch the boost test matrix though for windows/msvc test results.  
  
You might want to consider this when setting up your own testing.  
  
I'm thinking what I need to do is try too run all this without the visibility-hidden.  It never occurred to me that one would want to skip visibility once I got i working.  
  
[Boost Library Status Automatic Test.pdf](https://github.com/boostorg/serialization/files/8082228/Boost.Library.Status.Automatic.Test.pdf)

---

## Comment 5

> Username: AdmiralPellaeon  
> Created at: 2022-02-18 07:46:30 UTC  
> Url: https://github.com/boostorg/serialization/issues/252#issuecomment-1044077885  

Thank you very much for your efforts. I will check the visibility option of b2.  
  
As a first solution, I recompiled boost as shared library. This also solves the conflict with the multiple symbol definition.
