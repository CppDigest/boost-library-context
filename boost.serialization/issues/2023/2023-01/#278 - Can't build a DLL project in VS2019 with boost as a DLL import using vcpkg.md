# #278 - Can't build a DLL project in VS2019 with boost as a DLL import using vcpkg [Closed]

> Username: danyhm  
> Created at: 2023-01-29 16:54:16 UTC  
> Updated at: 2023-02-02 07:44:49 UTC  
> Closed at: 2023-02-02 07:44:49 UTC  
> Url: https://github.com/boostorg/serialization/issues/278  

Hello,  
  
I'm using boost serialization with vcpkg and I can build a project using static import in vcpkg configuration but I can't use it as a dynamic library.  
and I get the following errors:  
  
```  
Severity	Code	Description	Project	File	Line	Suppression State  
Error	C2491	'boost::archive::detail::archive_serializer_map<Archive>::insert': definition of dllimport function not allowed	hwidtool_DLL	C:\dev\vcpkg\installed\x86-windows\include\boost\archive\impl\archive_serializer_map.ipp	41	  
Error	C2491	'boost::archive::detail::archive_serializer_map<Archive>::erase': definition of dllimport function not allowed	hwidtool_DLL	C:\dev\vcpkg\installed\x86-windows\include\boost\archive\impl\archive_serializer_map.ipp	49	  
Error	C2491	'boost::archive::detail::archive_serializer_map<Archive>::find': definition of dllimport function not allowed	hwidtool_DLL	C:\dev\vcpkg\installed\x86-windows\include\boost\archive\impl\archive_serializer_map.ipp	65	  
Error	C2491	'boost::archive::basic_binary_iprimitive<Archive,Elem,Tr>::init': definition of dllimport function not allowed	hwidtool_DLL	C:\dev\vcpkg\installed\x86-windows\include\boost\archive\impl\basic_binary_iprimitive.ipp	36	  
Error	C2491	'boost::archive::basic_binary_iprimitive<Archive,Elem,Tr>::load': definition of dllimport function not allowed	hwidtool_DLL	C:\dev\vcpkg\installed\x86-windows\include\boost\archive\impl\basic_binary_iprimitive.ipp	91	  
Error	C2491	'boost::archive::basic_binary_iprimitive<Archive,Elem,Tr>::load': definition of dllimport function not allowed	hwidtool_DLL	C:\dev\vcpkg\installed\x86-windows\include\boost\archive\impl\basic_binary_iprimitive.ipp	103	  
Error	C2491	'boost::archive::basic_binary_iprimitive<Archive,Elem,Tr>::load': definition of dllimport function not allowed	hwidtool_DLL	C:\dev\vcpkg\installed\x86-windows\include\boost\archive\impl\basic_binary_iprimitive.ipp	119	  
Error	C2491	'boost::archive::basic_binary_iprimitive<Archive,Elem,Tr>::load': definition of dllimport function not allowed	hwidtool_DLL	C:\dev\vcpkg\installed\x86-windows\include\boost\archive\impl\basic_binary_iprimitive.ipp	130	  
Error	C2491	'boost::archive::basic_binary_iprimitive<Archive,Elem,Tr>::basic_binary_iprimitive': definition of dllimport function not allowed	hwidtool_DLL	C:\dev\vcpkg\installed\x86-windows\include\boost\archive\impl\basic_binary_iprimitive.ipp	146	  
Error	C2491	'boost::archive::basic_binary_iprimitive<Archive,Elem,Tr>::~basic_binary_iprimitive': definition of dllimport function not allowed	hwidtool_DLL	C:\dev\vcpkg\installed\x86-windows\include\boost\archive\impl\basic_binary_iprimitive.ipp	170	  
Error	C2491	'boost::archive::detail::archive_serializer_map<Archive>::insert': definition of dllimport function not allowed	hwidtool_DLL	C:\dev\vcpkg\installed\x86-windows\include\boost\archive\impl\archive_serializer_map.ipp	41	  
Error	C2491	'boost::archive::detail::archive_serializer_map<Archive>::erase': definition of dllimport function not allowed	hwidtool_DLL	C:\dev\vcpkg\installed\x86-windows\include\boost\archive\impl\archive_serializer_map.ipp	49	  
Error	C2491	'boost::archive::detail::archive_serializer_map<Archive>::find': definition of dllimport function not allowed	hwidtool_DLL	C:\dev\vcpkg\installed\x86-windows\include\boost\archive\impl\archive_serializer_map.ipp	65	  
Error	C2491	'boost::archive::basic_binary_oprimitive<Archive,Elem,Tr>::init': definition of dllimport function not allowed	hwidtool_DLL	C:\dev\vcpkg\installed\x86-windows\include\boost\archive\impl\basic_binary_oprimitive.ipp	41	  
Error	C2491	'boost::archive::basic_binary_oprimitive<Archive,Elem,Tr>::save': definition of dllimport function not allowed	hwidtool_DLL	C:\dev\vcpkg\installed\x86-windows\include\boost\archive\impl\basic_binary_oprimitive.ipp	57	  
Error	C2491	'boost::archive::basic_binary_oprimitive<Archive,Elem,Tr>::save': definition of dllimport function not allowed	hwidtool_DLL	C:\dev\vcpkg\installed\x86-windows\include\boost\archive\impl\basic_binary_oprimitive.ipp	66	  
Error	C2491	'boost::archive::basic_binary_oprimitive<Archive,Elem,Tr>::save': definition of dllimport function not allowed	hwidtool_DLL	C:\dev\vcpkg\installed\x86-windows\include\boost\archive\impl\basic_binary_oprimitive.ipp	77	  
Error	C2491	'boost::archive::basic_binary_oprimitive<Archive,Elem,Tr>::save': definition of dllimport function not allowed	hwidtool_DLL	C:\dev\vcpkg\installed\x86-windows\include\boost\archive\impl\basic_binary_oprimitive.ipp	88	  
Error	C2491	'boost::archive::basic_binary_oprimitive<Archive,Elem,Tr>::basic_binary_oprimitive': definition of dllimport function not allowed	hwidtool_DLL	C:\dev\vcpkg\installed\x86-windows\include\boost\archive\impl\basic_binary_oprimitive.ipp	99	  
Error	C2491	'boost::archive::basic_binary_oprimitive<Archive,Elem,Tr>::~basic_binary_oprimitive': definition of dllimport function not allowed	hwidtool_DLL	C:\dev\vcpkg\installed\x86-windows\include\boost\archive\impl\basic_binary_oprimitive.ipp	123	  
  
```  
  
linking boost as a static library is undesirable. is this a problem on my side or is this by design?

---

## Comment 1

> Username: danyhm  
> Created at: 2023-01-30 09:52:23 UTC  
> Url: https://github.com/boostorg/serialization/issues/278#issuecomment-1408301756  

sorry this seems to be a problem with the `portable_binary_iarchive` and `portable_binary_oarchive` files which are not part of the standard boost serialization library. any idea how this can be fixed?

---

## Comment 2

> Username: robertramey  
> Created at: 2023-01-30 11:47:56 UTC  
> Url: https://github.com/boostorg/serialization/issues/278#issuecomment-1408480335  

Just include the source files for portable by binary archive directly in your application.Robert Rameywww.rrsd.comOn Jan 30, 2023, at 1:52 AM, danyhm ***@***.***> wrote:﻿  
sorry this seems to be a problem with the portable_binary_iarchive and portable_binary_oarchive files which are not part of the standard boost serialization library. any idea how this can be fixed?  
  
—Reply to this email directly, view it on GitHub, or unsubscribe.You are receiving this because you are subscribed to this thread.Message ID: ***@***.***>

---

## Comment 3

> Username: danyhm  
> Created at: 2023-01-30 12:23:07 UTC  
> Url: https://github.com/boostorg/serialization/issues/278#issuecomment-1408524839  

Thank for the reponse Robert!  
  
I've already included the files in my project and it builds fine when using static libraries. However for dynamic libraries the compiler gives the errors mentioned above. I took a closer look and it seems the default boost library includes the correct macros for exported functions of the library but the portable_binary_archive files does not include those and some kind of redeclaration happens here. I'm not able to narrow the problem to which functions need them.

---

## Comment 4

> Username: robertramey  
> Created at: 2023-01-30 15:31:36 UTC  
> Url: https://github.com/boostorg/serialization/issues/278#issuecomment-1408834431  

Looks like the errors issue from the vcpkg build of the library.  I don't see how this would have anything to do with portable_binary_archive which is not part of the library.  The library can be built with boost b2 and CMake.  I've never tried to build the library with vcpkg build.  You might try building the library with CMake and/or b2.  Once built, the library should be linkable.  But still the "visibility" feature could be a problem.  It makes the binaries a lot smaller, but it's an add-on hack by the compiler vendors with inconsistent syntax between them.  This is a little outside of boost.  Open an issue with stack overflow and see if you get some useful information.
