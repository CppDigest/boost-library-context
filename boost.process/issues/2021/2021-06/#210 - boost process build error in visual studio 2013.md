# #210 - boost process build error in visual studio 2013 [Closed]

> Username: gvarun25  
> Created at: 2021-06-18 07:03:57 UTC  
> Updated at: 2021-06-18 07:49:22 UTC  
> Closed at: 2021-06-18 07:49:22 UTC  
> Url: https://github.com/boostorg/process/issues/210  

i have using  boost 1.76.0 version . I tried compiling in visual studio 2013.  
  
`#include <iostream>  
#include "boost/process.hpp"  
int main(int argc, char* argv[])  
{  
    boost::process::child *child;   
}  
`  
i tried above code. if i include `#include "boost/process.hpp` header i got build error from config.hpp file  
  
**Error:**  
  
`E:\Projects\build\third_party\include\boost-1_76\boost/process/detail/config.hpp(69): error C3646: 'noexcept' : unknown override specifier [E:\Projects\EXA_L  
inux\exa_services_linux\build\dicom\process_handler\process_handler.vcxproj]  
E:\Projects\build\third_party\include\boost-1_76\boost/process/detail/config.hpp(76): error C2660: 'boost::process::process_error::process_error' : function  
does not take 2 arguments [E:\Projects\build\dicom\process_handler\process_handler.vcxproj]  
E:\Projects\build\third_party\include\boost-1_76\boost/process/detail/config.hpp(81): error C2660: 'boost::process::process_error::process_error' : function  
does not take 2 arguments [E:\Projects\build\dicom\process_handler\process_handler.vcxproj]  
E:\Projects\build\third_party\include\boost-1_76\boost/process/detail/config.hpp(86): error C2440: '<function-style-cast>' : cannot convert from 'std::error_  
code' to 'boost::process::process_error' [E:\Projects\build\dicom\process_handler\process_handler.vcxproj]  
          No constructor could take the source type, or constructor overload resolution was ambiguous  
E:\Projects\build\third_party\include\boost-1_76\boost/process/detail/config.hpp(92): error C2440: '<function-style-cast>' : cannot convert from 'const std::  
error_code' to 'boost::process::process_error' [E:\Projects\build\dicom\process_handler\process_handler.vcxproj]  
          No constructor could take the source type, or constructor overload resolution was ambiguous  
E:\Projects\build\third_party\include\boost-1_76\boost/process/detail/config.hpp(98): error C2660: 'boost::process::process_error::process_error' : function  
does not take 2 arguments [E:\Projects\build\dicom\process_handler\process_handler.vcxproj]  
E:\Projects\build\third_party\include\boost-1_76\boost/process/detail/config.hpp(101): error C2146: syntax error : missing ';' before identifier 'Char' [E:\P  
rojects\build\dicom\process_handler\process_handler.vcxproj]  
E:\Projects\build\third_party\include\boost-1_76\boost/process/detail/config.hpp(101): error C2146: syntax error : missing ';' before identifier 'null_char'  
[E:\Projects\build\dicom\process_handler\process_handler.vcxproj]  
E:\Projects\build\third_party\include\boost-1_76\boost/process/detail/config.hpp(101): error C4430: missing type specifier - int assumed. Note: C++ does not  
support default-int [E:\Projects\build\dicom\process_handler\process_handler.vcxproj]  
E:\Projects\build\third_party\include\boost-1_76\boost/process/detail/config.hpp(102): error C2144: syntax error : 'char' should be preceded by ';' [E:\Proje  
cts\build\dicom\process_handler\process_handler.vcxproj]  
E:\Projects\build\third_party\include\boost-1_76\boost/process/detail/config.hpp(102): error C2143: syntax error : missing ';' before '<' [E:\Projects\EXA_Li  
nux\exa_services_linux\build\dicom\process_handler\process_handler.vcxproj]  
E:\Projects\build\third_party\include\boost-1_76\boost/process/detail/config.hpp(103): error C2143: syntax error : missing ';' before '{' [E:\Projects\EXA_Li  
nux\exa_services_linux\build\dicom\process_handler\process_handler.vcxproj]  
E:\Projects\build\third_party\include\boost-1_76\boost/process/detail/config.hpp(103): error C2447: '{' : missing function header (old-style formal list?) [E  
:\Projects\build\dicom\process_handler\process_handler.vcxproj]  
E:\Projects\build\third_party\include\boost-1_76\boost/process/detail/config.hpp(105): error C2146: syntax error : missing ';' before identifier 'Char' [E:\P  
rojects\build\dicom\process_handler\process_handler.vcxproj]  
E:\Projects\build\third_party\include\boost-1_76\boost/process/detail/config.hpp(105): error C2146: syntax error : missing ';' before identifier 'equal_sign'  
 [E:\Projects\build\dicom\process_handler\process_handler.vcxproj]  
E:\Projects\build\third_party\include\boost-1_76\boost/process/detail/config.hpp(105): error C4430: missing type specifier - int assumed. Note: C++ does not  
support default-int [E:\Projects\build\dicom\process_handler\process_handler.vcxproj]  
  dicom_process_handler.cpp  
E:\Projects\build\third_party\include\boost-1_76\boost/config/compiler/visualc.hpp(173): warning C4005: 'BOOST_NO_CXX11_NOEXCEPT' : macro redefinition [E:\Pr  
ojects\build\dicom\process_handler\process_handler.vcxproj]  
          command-line arguments :  see previous definition of 'BOOST_NO_CXX11_NOEXCEPT'  
E:\Projects\build\third_party\include\boost-1_76\boost/process/detail/config.hpp(69): error C3646: 'noexcept' : unknown override specifier [E:\Projects\EXA_L  
inux\exa_services_linux\build\dicom\process_handler\process_handler.vcxproj]  
E:\Projects\build\third_party\include\boost-1_76\boost/process/detail/config.hpp(76): error C2660: 'boost::process::process_error::process_error' : function  
does not take 2 arguments [E:\Projects\build\dicom\process_handler\process_handler.vcxproj]  
E:\Projects\build\third_party\include\boost-1_76\boost/process/detail/config.hpp(81): error C2660: 'boost::process::process_error::process_error' : function  
does not take 2 arguments [E:\Projects\build\dicom\process_handler\process_handler.vcxproj]  
E:\Projects\build\third_party\include\boost-1_76\boost/process/detail/config.hpp(86): error C2440: '<function-style-cast>' : cannot convert from 'std::error_  
code' to 'boost::process::process_error' [E:\Projects\build\dicom\process_handler\process_handler.vcxproj]  
          No constructor could take the source type, or constructor overload resolution was ambiguous  
E:\Projects\build\third_party\include\boost-1_76\boost/process/detail/config.hpp(92): error C2440: '<function-style-cast>' : cannot convert from 'const std::  
error_code' to 'boost::process::process_error' [E:\Projects\build\dicom\process_handler\process_handler.vcxproj]  
          No constructor could take the source type, or constructor overload resolution was ambiguous  
E:\Projects\build\third_party\include\boost-1_76\boost/process/detail/config.hpp(98): error C2660: 'boost::process::process_error::process_error' : function  
does not take 2 arguments [E:\Projects\build\dicom\process_handler\process_handler.vcxproj]  
E:\Projects\build\third_party\include\boost-1_76\boost/process/detail/config.hpp(101): error C2146: syntax error : missing ';' before identifier 'Char' [E:\P  
rojects\build\dicom\process_handler\process_handler.vcxproj]  
E:\Projects\build\third_party\include\boost-1_76\boost/process/detail/config.hpp(101): error C2146: syntax error : missing ';' before identifier 'null_char'  
[E:\Projects\build\dicom\process_handler\process_handler.vcxproj]  
E:\Projects\build\third_party\include\boost-1_76\boost/process/detail/config.hpp(101): error C4430: missing type specifier - int assumed. Note: C++ does not  
support default-int [E:\Projects\build\dicom\process_handler\process_handler.vcxproj]  
E:\Projects\build\third_party\include\boost-1_76\boost/process/detail/config.hpp(102): error C2144: syntax error : 'char' should be preceded by ';' [E:\Proje  
cts\build\dicom\process_handler\process_handler.vcxproj]  
E:\Projects\build\third_party\include\boost-1_76\boost/process/detail/config.hpp(102): error C2143: syntax error : missing ';' before '<' [E:\Projects\EXA_Li  
nux\exa_services_linux\build\dicom\process_handler\process_handler.vcxproj]  
E:\Projects\build\third_party\include\boost-1_76\boost/process/detail/config.hpp(103): error C2143: syntax error : missing ';' before '{' [E:\Projects\EXA_Li  
nux\exa_services_linux\build\dicom\process_handler\process_handler.vcxproj]  
E:\Projects\build\third_party\include\boost-1_76\boost/process/detail/config.hpp(103): error C2447: '{' : missing function header (old-style formal list?) [E  
:\Projects\build\dicom\process_handler\process_handler.vcxproj]  
E:\Projects\build\third_party\include\boost-1_76\boost/process/detail/config.hpp(105): error C2146: syntax error : missing ';' before identifier 'Char' [E:\P  
rojects\build\dicom\process_handler\process_handler.vcxproj]  
E:\Projects\build\third_party\include\boost-1_76\boost/process/detail/config.hpp(105): error C2146: syntax error : missing ';' before identifier 'equal_sign'  
 [E:\Projects\build\dicom\process_handler\process_handler.vcxproj]  
E:\Projects\build\third_party\include\boost-1_76\boost/process/detail/config.hpp(105): error C4430: missing type specifier - int assumed. Note: C++ does not  
support default-int [E:\Projects\build\dicom\process_handler\process_handler.vcxproj]`  
  
and also i tried `BOOST_NO_CXX11_NOEXCEPT ` preprocessor.  
  
how to fix above issue?

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2021-06-18 07:49:22 UTC  
> Url: https://github.com/boostorg/process/issues/210#issuecomment-863835337  

Update your compiler, boost.process requires C++11.
