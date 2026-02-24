# #107 - develop build broken [Closed]

> Username: jzmaddock  
> Created at: 2018-05-05 07:27:25 UTC  
> Updated at: 2018-05-27 09:39:47 UTC  
> Closed at: 2018-05-27 09:39:47 UTC  
> Url: https://github.com/boostorg/serialization/issues/107  

Hi Robert,  
  
Something just broke the develop build on all msvc versions prior to 14.1, my appveyor CI tests are all failing in:  
  
```  
[00:10:40] compile-c-c++ ..\..\..\bin.v2\libs\serialization\build\9218ae764f8240b7321ebd1ea2d19264\xml_grammar.obj  
[00:10:40] xml_grammar.cpp  
[00:10:40] ..\..\..\boost/archive/iterators/xml_unescape.hpp(114) : fatal error C1063: compiler limit : compiler stack overflow  
[00:10:40] Internal Compiler Error in C:\Program Files (x86)\Microsoft Visual Studio 12.0\VC\BIN\cl.exe.  You will be prompted to send an error report to Microsoft later.  
[00:10:41]   
[00:10:41]     call "..\..\..\bin.v2\standalone\msvc\0b00851a9d61d40fad804f9053579ad8\msvc-setup.bat" x86 >nul  
[00:10:41]  cl /Zm800 -nologo @"..\..\..\bin.v2\libs\serialization\build\9218ae764f8240b7321ebd1ea2d19264\xml_grammar.obj.rsp"   
```

---

## Comment 1

> Username: robertramey  
> Created at: 2018-05-06 16:52:55 UTC  
> Url: https://github.com/boostorg/serialization/issues/107#issuecomment-386893052  

OK - I think I fixed this

---

## Comment 2

> Username: jeking3  
> Created at: 2018-05-07 11:52:28 UTC  
> Url: https://github.com/boostorg/serialization/issues/107#issuecomment-387041206  

Seeing the same thing with MSVC2010 builds in uuid:  
```  
[00:03:41] compile-c-c++ bin.v2\libs\serialization\build\msvc-10.0\release\cxxstd-03-iso\link-static\threading-multi\xml_grammar.obj  
[00:03:41] xml_grammar.cpp  
[00:03:41] .\boost/archive/iterators/xml_unescape.hpp(121) : fatal error C1063: compiler limit : compiler stack overflow  
[00:03:41] Internal Compiler Error in c:\Program Files (x86)\Microsoft Visual Studio 10.0\VC\BIN\cl.exe.  You will be prompted to send an error report to Microsoft later.  
[00:03:41]   
```  
  
I will try to update to the latest serialization and see what happens.

---

## Comment 3

> Username: jeking3  
> Created at: 2018-05-08 11:42:07 UTC  
> Updated at: 2018-05-08 17:50:05 UTC  
> Url: https://github.com/boostorg/serialization/issues/107#issuecomment-387375184  

Still seeing this as of last night:  
  
https://ci.appveyor.com/project/jeking3/uuid-gaamf/build/1.0.116-develop/job/b1py91x9f3rs5wne  
https://ci.appveyor.com/project/jeking3/uuid-gaamf/build/1.0.117-develop/job/m9c937ap8v8d81iq

---

## Comment 4

> Username: jzmaddock  
> Created at: 2018-05-12 08:38:50 UTC  
> Url: https://github.com/boostorg/serialization/issues/107#issuecomment-388540149  

Me too.  
  
However, this can be fixed by making `xml_unescape<>::drain` and `xml_unescape::drain_residue` class inline.  
  
The problem appears to be the definition of `xml_unescape::value_type` which actually looks kind of circular to me:  
  
```  
        typedef typename this_t::value_type value_type;  
```  
  
which defines value_type in terms of itself?

---

## Comment 5

> Username: jzmaddock  
> Created at: 2018-05-12 08:43:04 UTC  
> Url: https://github.com/boostorg/serialization/issues/107#issuecomment-388540336  

Update, using:  
  
```  
        typedef int value_type;  
```  
  
Does fix the issue, but the BOOST_WORKAROUND uses the wrong value, MSVC-14 has BOOST_MSVC == 1900.  
  
I still don't see how:  
  
```  
typedef typename this_t::value_type value_type;  
```  
  
can compile though...

---

## Comment 6

> Username: jeking3  
> Created at: 2018-05-15 03:11:19 UTC  
> Url: https://github.com/boostorg/serialization/issues/107#issuecomment-389029203  

Still happening:  
```  
[00:03:21] .\boost/archive/iterators/xml_unescape.hpp(121) : fatal error C1063: compiler limit : compiler stack overflow  
[00:03:21] Internal Compiler Error in c:\Program Files (x86)\Microsoft Visual Studio 10.0\VC\BIN\cl.exe.  You will be prompted to send an error report to Microsoft later.  
[00:03:23]   
[00:03:23]     call "bin.v2\standalone\msvc\msvc-10.0\msvc-setup.bat" x86 >nul  
[00:03:23]  cl /Zm800 -nologo @"bin.v2\libs\serialization\build\msvc-10.0\release\cxxstd-03-iso\threading-multi\xml_grammar.obj.rsp"   
[00:03:23]   
[00:03:23] ...failed compile-c-c++ bin.v2\libs\serialization\build\msvc-10.0\release\cxxstd-03-iso\threading-multi\xml_grammar.obj...  
```  
  
ref: https://ci.appveyor.com/project/jeking3/date-time-1evbf/build/job/w89rydna0rgy8bn4

---

## Comment 7

> Username: jzmaddock  
> Created at: 2018-05-23 17:37:09 UTC  
> Url: https://github.com/boostorg/serialization/issues/107#issuecomment-391434985  

Ping?  Any chance the MSVC workaround version  can be changed to the correct value (1900)?

---

## Comment 8

> Username: jzmaddock  
> Created at: 2018-05-27 09:39:47 UTC  
> Url: https://github.com/boostorg/serialization/issues/107#issuecomment-392318239  

Confirmed this is now fixed in develop.  Thanks!
