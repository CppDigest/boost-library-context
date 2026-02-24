# #57 - clang-cl uses incorrect redeclaration of Sleep [Closed]

> Username: computerquip-streamlabs  
> Created at: 2018-11-28 23:43:01 UTC  
> Updated at: 2018-11-29 00:24:27 UTC  
> Closed at: 2018-11-29 00:24:27 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/57  

When compiling with clang-cl under Windows, it provides a warning concerning incorrect declaration.  
  
```  
2>In file included from D:\Libraries\boost_1_67_0\msvc141-1_67_0-all\include\boost-1_67\boost/filesystem.hpp:16:  
2>In file included from D:\Libraries\boost_1_67_0\msvc141-1_67_0-all\include\boost-1_67\boost/filesystem/path.hpp:29:  
2>In file included from D:\Libraries\boost_1_67_0\msvc141-1_67_0-all\include\boost-1_67\boost/shared_ptr.hpp:17:  
2>In file included from D:\Libraries\boost_1_67_0\msvc141-1_67_0-all\include\boost-1_67\boost/smart_ptr/shared_ptr.hpp:36:  
2>In file included from D:\Libraries\boost_1_67_0\msvc141-1_67_0-all\include\boost-1_67\boost/smart_ptr/detail/spinlock_pool.hpp:25:  
2>In file included from D:\Libraries\boost_1_67_0\msvc141-1_67_0-all\include\boost-1_67\boost/smart_ptr/detail/spinlock.hpp:47:  
2>In file included from D:\Libraries\boost_1_67_0\msvc141-1_67_0-all\include\boost-1_67\boost/smart_ptr/detail/spinlock_std_atomic.hpp:18:  
2>D:\Libraries\boost_1_67_0\msvc141-1_67_0-all\include\boost-1_67\boost/smart_ptr/detail/yield_k.hpp(64): warning : 'boost::detail::Sleep' redeclared without 'dllimport' attribute: previous 'dllimport' ignored [-Winconsistent-dllimport]  
2>C:\Program Files (x86)\Windows Kits\10\Include\10.0.17134.0\um\synchapi.h(750):  note: previous declaration is here  
2>C:\Program Files (x86)\Windows Kits\10\Include\10.0.17134.0\um\synchapi.h(747):  note: previous attribute is here  
2>C:\Program Files (x86)\Windows Kits\10\Include\10.0.17134.0\shared\apisetcconv.h(22):  note: expanded from macro 'WINBASEAPI'  
2>C:\Program Files (x86)\Windows Kits\10\Include\10.0.17134.0\um\winnt.h(184):  note: expanded from macro 'DECLSPEC_IMPORT'  
```  
  
This is probably caused by some assumption about clang and using the VC++ headers. Not totally sure.

---

## Comment 1

> Username: computerquip-streamlabs  
> Created at: 2018-11-29 00:24:27 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/57#issuecomment-442657960  

Seems that there were some changes between 1.67 and 1.68 that might have fixed this. I'll close this for now and wait until I update to re-open.
