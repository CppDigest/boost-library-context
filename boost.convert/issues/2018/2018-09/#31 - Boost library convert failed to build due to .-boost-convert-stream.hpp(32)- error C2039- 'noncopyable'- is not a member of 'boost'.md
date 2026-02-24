# #31 - Boost library convert failed to build due to .\boost/convert/stream.hpp(32): error C2039: 'noncopyable': is not a member of 'boost' [Closed]

> Username: KarenHuang2016  
> Created at: 2018-09-17 10:21:01 UTC  
> Updated at: 2018-09-23 20:20:23 UTC  
> Closed at: 2018-09-23 20:20:23 UTC  
> Url: https://github.com/boostorg/convert/issues/31  

**Environment:**  
VS 2017 + Windows Server 2016  
  
**Issue description:**  
We build and run test for Boost. And we found fallbacks.cpp failed to build due to error C2039: 'noncopyable': is not a member of 'boost'. After we added #include <boost/noncopyable.hpp> into ".\boost/convert/stream.hpp" . It build fine. Could you pleaes take a look? Thanks in advance.  
  
**Error info:**  
compile-c-c++ ..\out\x86rel\boost\bin.v2\libs\convert\test\convert_test_fallbacks.test\msvc-14.1\release\asynch-exceptions-on\threading-multi\fallbacks.obj  
fallbacks.cpp  
.\boost/convert/stream.hpp(32): error C2039: 'noncopyable': is not a member of 'boost'  
.\boost/bind/placeholders.hpp(26): note: see declaration of 'boost'  
libs\convert\test\fallbacks.cpp(37): note: see reference to class template instantiation 'boost::cnv::basic_stream<char>' being compiled  
.\boost/bind/placeholders.hpp(54): note: see reference to class template instantiation 'boost::arg<9>' being compiled  
.\boost/bind/placeholders.hpp(53): note: see reference to class template instantiation 'boost::arg<8>' being compiled  
.\boost/bind/placeholders.hpp(52): note: see reference to class template instantiation 'boost::arg<7>' being compiled  
.\boost/bind/placeholders.hpp(51): note: see reference to class template instantiation 'boost::arg<6>' being compiled  
.\boost/bind/placeholders.hpp(50): note: see reference to class template instantiation 'boost::arg<5>' being compiled  
.\boost/bind/placeholders.hpp(49): note: see reference to class template instantiation 'boost::arg<4>' being compiled  
.\boost/bind/placeholders.hpp(48): note: see reference to class template instantiation 'boost::arg<3>' being compiled  
.\boost/bind/placeholders.hpp(47): note: see reference to class template instantiation 'boost::arg<2>' being compiled  
.\boost/bind/placeholders.hpp(46): note: see reference to class template instantiation 'boost::arg<1>' being compiled  
.\boost/convert/stream.hpp(33): error C2504: 'noncopyable': base class undefined  
  
    call "..\out\x86rel\boost\bin.v2\standalone\msvc\msvc-14.1\msvc-setup.bat"  >nul  
 cl /Zm800 -nologo @"..\out\x86rel\boost\bin.v2\libs\convert\test\convert_test_fallbacks.test\msvc-14.1\release\asynch-exceptions-on\threading-multi\fallbacks.obj.rsp"   
  
...failed compile-c-c++ ..\out\x86rel\boost\bin.v2\libs\convert\test\convert_test_fallbacks.test\msvc-14.1\release\asynch-exceptions-on\threading-multi\fallbacks.obj...

---

## Comment 1

> Username: yet-another-user  
> Created at: 2018-09-17 21:53:47 UTC  
> Url: https://github.com/boostorg/convert/issues/31#issuecomment-422184450  

Added the missing header. Please see if that is to your satisfaction. Thank you for reporting.

---

## Comment 2

> Username: yet-another-user  
> Created at: 2018-09-23 20:20:23 UTC  
> Url: https://github.com/boostorg/convert/issues/31#issuecomment-423844977  

I presume the issue has been addressed. Closing.
