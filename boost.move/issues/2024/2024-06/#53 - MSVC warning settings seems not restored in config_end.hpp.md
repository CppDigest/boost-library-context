# #53 - MSVC warning settings seems not restored in config_end.hpp [Closed]

> Username: kazuhiro-nishimura  
> Created at: 2024-06-28 08:45:22 UTC  
> Updated at: 2024-08-18 21:50:59 UTC  
> Closed at: 2024-08-18 21:50:59 UTC  
> Url: https://github.com/boostorg/move/issues/53  

I found MSVC warning settings set in `config_begin.hpp` are not actually restored in `config_end.hpp`.  Manually adding `#pragma warning(push)` and `#pragma warning(pop)` in `<boost/move/utility.hpp>` resolved my problem.  
  
It is possible that the push and pop pragmas does not work unless used in the same header?  
  
https://github.com/boostorg/move/blob/c973467a31949a3dd6c1908a4ec267ea635cab07/include/boost/move/detail/config_end.hpp#L11

---

## Comment 1

> Username: kazuhiro-nishimura  
> Created at: 2024-07-01 02:28:54 UTC  
> Url: https://github.com/boostorg/move/issues/53#issuecomment-2199009148  

This is an example.  
  
```c++  
  
[[deprecated("Should be warned")]]  
void f()  
{}  
  
void g()  
{  
    f();  // This is warned.  
}  
  
#include <boost/move/utility.hpp>  
  
void h()  
{  
    f();  // This should also be warned but not.  
}  
```

---

## Comment 2

> Username: igaztanaga  
> Created at: 2024-07-05 08:39:42 UTC  
> Url: https://github.com/boostorg/move/issues/53#issuecomment-2210452377  

In Visual 2022 with the latest version of Boost, (which will become Boost 1.86 shortly) the expected output is printed:  
  
```  
1>...\boost\libs\move\test\move.cpp(7,4): warning C4996: 'f': Should be warned  
1>...\boost\libs\move\test\move.cpp(14,4): warning C4996: 'f': Should be warned  
```  
Which Visual Studio and Boost version are you using?

---

## Comment 3

> Username: kazuhiro-nishimura  
> Created at: 2024-07-05 08:50:59 UTC  
> Url: https://github.com/boostorg/move/issues/53#issuecomment-2210470105  

> In Visual 2022 with the latest version of Boost, (which will become Boost 1.86 shortly) the expected output is printed:  
>   
> ```  
> 1>...\boost\libs\move\test\move.cpp(7,4): warning C4996: 'f': Should be warned  
> 1>...\boost\libs\move\test\move.cpp(14,4): warning C4996: 'f': Should be warned  
> ```  
>   
> Which Visual Studio and Boost version are you using?  
  
Sorry for the delay. I'm using Visual Studio 2019 Version 16.11.37 with Boost 1.84.0 built by vcpkg.  
  
---  
  
Microsoft Visual Studio Professional 2019  
Version 16.11.37  
VisualStudio.16.Release/16.11.37+34931.43  
Microsoft .NET Framework  
Version 4.8.09032  
  
Installed Version: Professional  
  
Visual C++ 2019   00435-20151-39271-AA819  
Microsoft Visual C++ 2019  
  
ASP.NET and Web Tools 2019   16.11.119.57942  
ASP.NET and Web Tools 2019  
  
Azure App Service Tools v3.0.0   16.11.119.57942  
Azure App Service Tools v3.0.0  
  
C# Tools   3.11.0-4.22108.8+d9bef045c4362fbcab27ef35daec4e95c8ff47e1  
C# components used in the IDE. Depending on your project type and settings, a different version of the compiler may be used.  
  
Common Azure Tools   1.10  
Provides common services for use by Azure Mobile Services and Microsoft Azure Tools.  
  
IntelliCode Extension   1.0  
IntelliCode Visual Studio Extension Detailed Info  
  
Markdown Editor   1.12.253  
A full featured Markdown editor with live preview and syntax highlighting. Supports GitHub flavored Markdown.  
  
Microsoft JVM Debugger   1.0  
Provides support for connecting the Visual Studio debugger to JDWP compatible Java Virtual Machines  
  
Microsoft MI-Based Debugger   1.0  
Provides support for connecting Visual Studio to MI compatible debuggers  
  
Microsoft Visual C++ Wizards   1.0  
Microsoft Visual C++ Wizards  
  
Microsoft Visual Studio VC Package   1.0  
Microsoft Visual Studio VC Package  
  
NuGet Package Manager   5.11.5  
NuGet Package Manager in Visual Studio. For more information about NuGet, visit https://docs.nuget.org/  
  
ProjectServicesPackage Extension   1.0  
ProjectServicesPackage Visual Studio Extension Detailed Info  
  
SonarLint for Visual Studio   7.8.1.91542  
SonarLint is an extension to your favorite IDE that provides on-the-fly feedback to developers on new bugs and quality issues injected into their code.  
  
Test Adapter for Boost.Test   1.0  
Enables Visual Studio's testing tools with unit tests written for Boost.Test.  The use terms and Third Party Notices are available in the extension installation directory.  
  
Test Adapter for Google Test   1.0  
Enables Visual Studio's testing tools with unit tests written for Google Test.  The use terms and Third Party Notices are available in the extension installation directory.  
  
TypeScript Tools   16.0.30526.2002  
TypeScript Tools for Microsoft Visual Studio  
  
Visual Basic Tools   3.11.0-4.22108.8+d9bef045c4362fbcab27ef35daec4e95c8ff47e1  
Visual Basic components used in the IDE. Depending on your project type and settings, a different version of the compiler may be used.  
  
Visual Studio Code Debug Adapter Host Package   1.0  
Interop layer for hosting Visual Studio Code debug adapters in Visual Studio  
  
Visual Studio Tools for CMake   1.0  
Visual Studio Tools for CMake

---

## Comment 4

> Username: igaztanaga  
> Created at: 2024-07-06 18:51:17 UTC  
> Url: https://github.com/boostorg/move/issues/53#issuecomment-2211858249  

Compiling the latest version with Visual 2019 gives the correct output:  
  
1>boost\libs\move\test\move.cpp(7,4): warning C4996: 'f': Should be warned  
1>boost\libs\move\test\move.cpp(14,4): warning C4996: 'f': Should be warned  
  
I also tested with Visual 2017. So it seems that the issue is already fixed. Not sure if it's easy for you to confirm this.

---

## Comment 5

> Username: kazuhiro-nishimura  
> Created at: 2024-07-08 01:57:53 UTC  
> Url: https://github.com/boostorg/move/issues/53#issuecomment-2212799926  

Thank you.   
  
I finally found this issue occurred only if Boost headers are included from the project's **External Include Directories** path instead of Include Directories. It is possible that the stack is reset for each external include.

---

## Comment 6

> Username: igaztanaga  
> Created at: 2024-08-18 21:50:59 UTC  
> Url: https://github.com/boostorg/move/issues/53#issuecomment-2295407839  

Closing this issue after workaround is found.
