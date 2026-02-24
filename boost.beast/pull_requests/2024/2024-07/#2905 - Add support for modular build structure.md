# #2905 Add support for modular build structure. [Merged]

> Username: grafikrobot  
> Created at: 2024-07-20 22:33:23 UTC  
> Updated at: 2024-09-01 07:56:03 UTC  
> Merged at: 2024-09-01 07:56:02 UTC  
> Closed at: 2024-09-01 07:56:02 UTC  
> Url: https://github.com/boostorg/beast/pull/2905  

This is part of the effort to make the Boost libraries "modular" for build and consumption. See https://lists.boost.org/Archives/boost/2024/01/255704.php and https://github.com/grafikrobot/boost-b2-modular/blob/b2-modular/README.adoc for more information.  
  
This PR depends on the following other PRs being merged to both develop and master branches of the respective repos:  
  
- https://github.com/boostorg/boost/pull/854  
  
This PR will be changed to ready for review, i.e. not draft, when the above are merged. Do not merge this one until that time.

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2024-07-27 16:23:12 UTC  
> Url: https://github.com/boostorg/beast/pull/2905#issuecomment-2254192223  

An automated preview of the documentation is available at [https://2905.beastdocs.prtest.cppalliance.org/libs/beast/doc/html/index.html](https://2905.beastdocs.prtest.cppalliance.org/libs/beast/doc/html/index.html)

---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2024-07-30 13:48:15 UTC  
> Url: https://github.com/boostorg/beast/pull/2905#issuecomment-2258398331  

An automated preview of the documentation is available at [https://2905.beastdocs.prtest.cppalliance.org/libs/beast/doc/html/index.html](https://2905.beastdocs.prtest.cppalliance.org/libs/beast/doc/html/index.html)

---

## Comment 3

> Username: cppalliance-bot  
> Created_at: 2024-08-09 12:18:20 UTC  
> Url: https://github.com/boostorg/beast/pull/2905#issuecomment-2277818301  

An automated preview of the documentation is available at [https://2905.beastdocs.prtest.cppalliance.org/libs/beast/doc/html/index.html](https://2905.beastdocs.prtest.cppalliance.org/libs/beast/doc/html/index.html)

---

## Comment 4

> Username: cppalliance-bot  
> Created_at: 2024-08-12 13:38:14 UTC  
> Url: https://github.com/boostorg/beast/pull/2905#issuecomment-2284021073  

An automated preview of the documentation is available at [https://2905.beastdocs.prtest.cppalliance.org/libs/beast/doc/html/index.html](https://2905.beastdocs.prtest.cppalliance.org/libs/beast/doc/html/index.html)

---

## Comment 5

> Username: grafikrobot  
> Created_at: 2024-08-18 16:05:01 UTC  
> Url: https://github.com/boostorg/beast/pull/2905#issuecomment-2295311647  

Please review and merge this PR at your earliest convenience.

---

## Comment 6

> Username: cppalliance-bot  
> Created_at: 2024-08-22 15:18:35 UTC  
> Url: https://github.com/boostorg/beast/pull/2905#issuecomment-2304972064  

An automated preview of the documentation is available at [https://2905.beastdocs.prtest.cppalliance.org/libs/beast/doc/html/index.html](https://2905.beastdocs.prtest.cppalliance.org/libs/beast/doc/html/index.html)

---

## Review 7 [Commented]

> Username: ashtum  
> Created_at: 2024-08-23 15:36:49 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/2905#pullrequestreview-2257505987  

📁 build/Jamfile

```diff
  18 |+         openssl-root = "C:/OpenSSL" ;
  19 |+     }
  20 |+ }
```

> Username: ashtum  
> Created_at: 2024-08-23 15:19:55 UTC  
> Updated_at: 2024-08-23 15:36:49 UTC  
> Url: https://github.com/boostorg/beast/pull/2905#discussion_r1729144538  

Can we add these directly to `openssl.jam`?

> Username: ashtum  
> Created_at: 2024-08-28 13:10:43 UTC  
> Updated_at: 2024-08-28 13:10:44 UTC  
> Url: https://github.com/boostorg/beast/pull/2905#discussion_r1734649917  

@grafikrobot

> Username: grafikrobot  
> Created_at: 2024-08-31 19:16:07 UTC  
> Url: https://github.com/boostorg/beast/pull/2905#discussion_r1739907889  

We could. Please add a feature request for that.. https://github.com/bfgroup/b2/issues  
  
But since that would require a new B2 release, which will not happen soon, it would be good to keep this for now (as it was already in the original Jamfile also).


📁 example/http/client/body/Jamfile

```diff
  12 |     <variant>coverage:<build>no
  13 |     <variant>ubasan:<build>no
  14 |+     <library>/boost/json//boost_json
```

> Username: ashtum  
> Created_at: 2024-08-23 15:36:36 UTC  
> Updated_at: 2024-08-23 15:36:49 UTC  
> Url: https://github.com/boostorg/beast/pull/2905#discussion_r1729166200  

This causes a link error because we aslo include `<boost/json/src.hpp>` in `json_client.cpp`.  
  
```  
compile-c-c++ ..\..\bin.v2\libs\beast\example\http\client\body\msvc-14.1\release\x86_64\cxxstd-17-iso\threading-multi\json_client.obj  
json_client.cpp  
..\..\boost/json/impl/array.ipp(31): warning C4273: 'empty_': inconsistent dll linkage  
..\..\boost/json/array.hpp(96): note: see previous definition of 'private: static boost::json::array::table boost::json::array::empty_'  
..\..\boost/json/impl/array.ipp(31): error C2491: 'boost::json::array::empty_': definition of dllimport static data member not allowed  
..\..\boost/json/impl/array.ipp(40): warning C4273: 'boost::json::array::table::allocate': inconsistent dll linkage  
..\..\boost/json/impl/array.hpp(42): note: see previous definition of 'allocate'  
..\..\boost/json/impl/array.ipp(63): warning C4273: 'boost::json::array::table::deallocate': inconsistent dll linkage  
...  
```

> Username: grafikrobot  
> Created_at: 2024-08-31 19:28:09 UTC  
> Updated_at: 2024-08-31 19:28:10 UTC  
> Url: https://github.com/boostorg/beast/pull/2905#discussion_r1739909494  

Thanks for the pointer. Fixed in latest.


---

## Comment 8

> Username: cppalliance-bot  
> Created_at: 2024-08-31 19:38:12 UTC  
> Url: https://github.com/boostorg/beast/pull/2905#issuecomment-2323025506  

An automated preview of the documentation is available at [https://2905.beastdocs.prtest.cppalliance.org/libs/beast/doc/html/index.html](https://2905.beastdocs.prtest.cppalliance.org/libs/beast/doc/html/index.html)

---

## Review 9 [Commented]

> Username: ashtum  
> Created_at: 2024-09-01 07:23:19 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/2905#pullrequestreview-2274227584  

📁 example/http/client/body/Jamfile

```diff
  12 |     <variant>coverage:<build>no
  13 |     <variant>ubasan:<build>no
  14 |+     <use>/boost/json//boost_json
```

> Username: ashtum  
> Created_at: 2024-09-01 07:23:18 UTC  
> Updated_at: 2024-09-01 07:25:58 UTC  
> Url: https://github.com/boostorg/beast/pull/2905#discussion_r1739993403  

This still passes `-DBOOST_JSON_DYN_LINK=1`:  
```  
 cl /Zm800 -nologo "json_client.cpp" -c -Fo"..\..\..\..\..\..\bin.v2\libs\beast\example\http\client\body\msvc-14.3\debug\x86_64\threading-multi\json_client.obj"     -TP /wd4675 /EHs /GR /Zc:throwingNew /Z7 /Od /Ob0 /W3 /MDd /Zc:forScope /Zc:wchar_t /Zc:inline /favor:blend /bigobj -DBOOST_ALL_NO_LIB=1 -DBOOST_ASIO_DISABLE_BOOST_ARRAY=1 -DBOOST_ASIO_DISABLE_BOOST_BIND=1 -DBOOST_ASIO_DISABLE_BOOST_DATE_TIME=1 -DBOOST_ASIO_DISABLE_BOOST_REGEX=1 -DBOOST_ASIO_NO_DEPRECATED=1 -DBOOST_ASIO_SEPARATE_COMPILATION -DBOOST_BEAST_ALLOW_DEPRECATED -DBOOST_BEAST_SEPARATE_COMPILATION -DBOOST_COBALT_USE_STD_PMR=1 -DBOOST_CONTAINER_DYN_LINK=1 -DBOOST_CONTAINER_NO_LIB=1 -DBOOST_COROUTINES_NO_DEPRECATION_WARNING=1 -DBOOST_JSON_DYN_LINK=1 -D_CRT_SECURE_NO_WARNINGS=1 -D_SCL_SECURE_NO_WARNINGS -D_SCL_SECURE_NO_WARNINGS=1 -D_SILENCE_CXX17_ADAPTOR_TYPEDEFS_DEPRECATION_WARNING -D_SILENCE_CXX17_ALLOCATOR_VOID_DEPRECATION_WARNING -D_WIN32_WINNT=0x0601 "-I..\..\..\..\..\.." "-I..\..\..\..\..\..\libs\beast" "-IC:\Users\win\Desktop\boost\libs\assert\include" "-IC:\Users\win\Desktop\boost\libs\container\include" "-IC:\Users\win\Desktop\boost\libs\intrusive\include" "-IC:\Users\win\Desktop\boost\libs\move\include"  
```  
And the same link errrors:  
```  
C:\Users\win\Desktop\boost\boost/json/impl/array.ipp(31): warning C4273: 'empty_': inconsistent dll linkage  
C:\Users\win\Desktop\boost\boost/json/array.hpp(96): note: see previous definition of 'empty_'  
C:\Users\win\Desktop\boost\boost/json/impl/array.ipp(31): error C2491: 'boost::json::array::empty_': definition of dllimport static data member not allowed  
C:\Users\win\Desktop\boost\boost/json/impl/array.ipp(36): warning C4273: 'boost::json::array::table::allocate': inconsistent dll linkage  
C:\Users\win\Desktop\boost\boost/json/impl/array.hpp(42): note: see previous definition of 'boost::json::array::table::allocate'  
C:\Users\win\Desktop\boost\boost/json/impl/array.ipp(60): warning C4273: 'boost::json::array::table::deallocate': inconsistent dll linkage  
C:\Users\win\Desktop\boost\boost/json/impl/array.hpp(49): note: see previous definition of 'boost::json::array::table::deallocate'  
C:\Users\win\Desktop\boost\boost/json/impl/array.ipp(76): warning C4273: 'boost::json::array::revert_insert::revert_insert': inconsistent dll linkage  
C:\Users\win\Desktop\boost\boost/json/impl/array.hpp(93): note: see previous definition of 'boost::json::array::revert_insert::revert_insert'  
C:\Users\win\Desktop\boost\boost/json/impl/array.ipp(128): warning C4273: 'boost::json::array::revert_insert::~revert_insert': inconsistent dll linkage  
C:\Users\win\Desktop\boost\boost/json/impl/array.hpp(100): note: see previous definition of 'boost::json::array::revert_insert::~revert_insert'  
```


---
