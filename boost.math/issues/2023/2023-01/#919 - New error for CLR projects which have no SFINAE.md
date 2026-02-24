# #919 - New error for CLR projects which have no SFINAE [Closed]

> Username: greenkalx  
> Created at: 2023-01-12 19:29:01 UTC  
> Updated at: 2023-01-13 22:20:23 UTC  
> Closed at: 2023-01-12 19:50:19 UTC  
> Url: https://github.com/boostorg/math/issues/919  

For Windows managed C++ CLR projects, the _M_CEE flag is set, which then sets BOOST_NO_SFINAE_EXPR in visualc.hpp.  
This results in a new compile error in cxx03_warn.hpp:  
`1>c:\work\external\libs\Boost\boost_1_81_0\boost\math\tools\cxx03_warn.hpp(92,1): fatal  error C1189: #error:  Support for C++03 has been removed. The minimum requirement for this library is fully compliant C++11.  
`  
  
So CLR projects are no longer supported?  
Can I set a flag to exclude features that use SFINAE?  
  
My temporary workaround is to comment out the BOOST_NO_SFINAE_EXPR check in cxx03_warn.hpp:  
```  
//#if defined(BOOST_NO_SFINAE_EXPR) && !defined(BOOST_MATH_SHOW_CXX03_WARNING)  
//#  define BOOST_MATH_SHOW_CXX03_WARNING  
//#  define BOOST_MATH_CXX03_WARN_REASON "BOOST_NO_SFINAE_EXPR"  
//#endif  
```  
  
System is Windows 10, compiling with Visual Studio 2022 C++17.  
With Boost v1.81.0 (upgrading from v1.65.1)

---

## Comment 1

> Username: greenkalx  
> Created at: 2023-01-12 19:37:01 UTC  
> Url: https://github.com/boostorg/math/issues/919#issuecomment-1380913038  

BOOST_NO_SFINAE_EXPR seems the only flag that triggers the error in cxx03_warn.hpp, none of the other BOOST_NO_CXX11_* flags trigger it.

---

## Comment 2

> Username: mborland  
> Created at: 2023-01-12 19:41:34 UTC  
> Url: https://github.com/boostorg/math/issues/919#issuecomment-1380917326  

@jzmaddock This should be an issue for Boost.Config correct? All of the `BOOST_NO_CXX11_*` and `BOOST_NO_SFINAE_EXPR` come directly from there, and we make no attempt to define them for standalone mode.

---

## Comment 3

> Username: greenkalx  
> Created at: 2023-01-12 19:50:19 UTC  
> Url: https://github.com/boostorg/math/issues/919#issuecomment-1380925350  

Thank you, moved to Boost.Config:  
https://github.com/boostorg/config/issues/467

---

## Comment 4

> Username: jzmaddock  
> Created at: 2023-01-13 09:12:12 UTC  
> Url: https://github.com/boostorg/math/issues/919#issuecomment-1381516129  

I'll check this out, the CLR compiler used to be pretty brain dead but may have improved, we'll see.

---

## Comment 5

> Username: greenkalx  
> Created at: 2023-01-13 22:20:23 UTC  
> Url: https://github.com/boostorg/math/issues/919#issuecomment-1382457774  

Since VS2017, we were able to remove overloaded constructors in C# which C++ could not previously find.  
When updating from VS2017 to VS2022, this resulted in the error:  
```  
1>  C:\work\acc_\GuiClr\include\GuiClr\App\NotificationManager.h(85,33): error C2668: 'GuiClr::Command::Command': ambiguous call to overloaded function  
1>  C:\work\acc_\GuiClr\include\GuiClr\App\NotificationManager.h(86): message : could be 'GuiClr::Command::Command(GuiClr::VoidHandler ^,GuiClr::ActionWithArgsHandler ^,GuiClr::IsValidHandler ^,System::String ^,System::Windows::Media::ImageSource ^,System::String ^,System::Windows::Media::DrawingGroup ^)'  
1>  C:\work\acc_\GuiClr\include\GuiClr\App\NotificationManager.h(86): message : or       'GuiClr::Command::Command(GuiClr::VoidHandler ^)'  
1>  C:\work\acc_\GuiClr\include\GuiClr\App\NotificationManager.h(86,1): message : while trying to match the argument list '(GuiClr::VoidHandler ^)'  
```  
Removing a Command constructor in Command.cs resolved this.  
  
This was fixed in a later VS2017, or V2019, or VS2022:  
https://learn.microsoft.com/en-us/cpp/overview/cpp-conformance-improvements-2017?view=msvc-170  
https://learn.microsoft.com/en-us/cpp/overview/cpp-conformance-improvements-2019?view=msvc-170  
https://learn.microsoft.com/en-us/cpp/overview/cpp-conformance-improvements?view=msvc-170  
  
There are some SFINAE fixes in those links.
