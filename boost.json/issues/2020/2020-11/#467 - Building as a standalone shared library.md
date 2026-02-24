# #467 - Building as a standalone shared library [Closed]

> Username: ennis  
> Created at: 2020-11-17 16:54:56 UTC  
> Updated at: 2020-11-18 15:22:57 UTC  
> Closed at: 2020-11-18 15:22:57 UTC  
> Url: https://github.com/boostorg/json/issues/467  

I was trying to build boost.json as a standalone shared library, using the provided CMake targets, and by defining  
```  
BUILD_SHARED_LIBS=ON  
BOOST_JSON_STANDALONE=ON  
```  
  
However the resulting library does not seem to export any symbol (I'm building with MSVC and no .lib file is generated).  
In include/boost/json/detail/config.hpp : https://github.com/boostorg/json/blob/099a82957a75bf088a8bc45a379f81b3c6661d18/include/boost/json/detail/config.hpp#L154-L183  
It seems that in the BOOST_JSON_STANDALONE case nothing is defined for BOOST_JSON_DECL and BOOST_JSON_CLASS_DECL and nothing is exported (I'd expect those to expand to something like `__declspec(dllexport)` on MSVC).  
  
The [documentation](https://www.boost.org/doc/libs/develop/libs/json/doc/html/json/overview.html#json.overview.requirements.without_boost) suggests that this should be a supported build configuration.  
  
Context: I was trying to build boost.json as a shared library because of a runtime error in `boost::json::standalone::detail::default_resource::do_deallocate` when returing an empty JSON array across a DLL boundary.  
https://github.com/boostorg/json/blob/099a82957a75bf088a8bc45a379f81b3c6661d18/include/boost/json/impl/array.ipp#L54-L67  
  
`p == &empty_` may be false if p points to the empty array object that was defined in another module (when linking statically). In this case the following line will try to delete an invalid pointer. This might be a separate issue, but I'm not sure whether passing boost.json objects across module boundaries (creating in one and deleting in another) is supported.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2020-11-17 17:42:35 UTC  
> Url: https://github.com/boostorg/json/issues/467#issuecomment-729090368  

We could probably support the cross-module use-case by changing  
```  
if(p == &empty_)   
```  
to  
```  
if( p->capacity == 0 )  
```

---

## Comment 2

> Username: vinniefalco  
> Created at: 2020-11-17 17:51:02 UTC  
> Url: https://github.com/boostorg/json/issues/467#issuecomment-729095392  

I don't know if Boost.JSON can support building a shared library in standalone. Doing so would require copying a lot of Boost.Config for the various compliers and toolchains. I think instead, we might make it the user's responsibility to define these macros appropriately:  
```  
BOOST_JSON_DECL  
BOOST_JSON_CLASS_DECL  
```

---

## Comment 3

> Username: vinniefalco  
> Created at: 2020-11-17 17:57:05 UTC  
> Url: https://github.com/boostorg/json/issues/467#issuecomment-729099990  

Can you please have a look at these commits and let me know if they address your use-case?  
https://github.com/vinniefalco/json/commit/4b6e888dbfa3ab0be5a6f52cc85d2135f0d7eea7  
https://github.com/vinniefalco/json/commit/80810a38758020bed5159fa4897bc9d9695c1c66

---

## Comment 4

> Username: ennis  
> Created at: 2020-11-17 18:47:37 UTC  
> Url: https://github.com/boostorg/json/issues/467#issuecomment-729128206  

Thank you for the quick reply.  
  
https://github.com/vinniefalco/json/commit/80810a38758020bed5159fa4897bc9d9695c1c66 seems to fix my second issue.  
  
https://github.com/vinniefalco/json/commit/4b6e888dbfa3ab0be5a6f52cc85d2135f0d7eea7 does not work when linking statically because I use other boost headers in my project that define `BOOST_SYMBOL_IMPORT`. The definitions in boost.json headers end up annotated with `__declspec(dllimport)` while the library itself is linked statically, which causes linker errors.  
Note that this is a somewhat contrived use case (dynamically-linked boost and statically-linked boost.json used at the same time), but maybe a separate macro would be more appropriate for the standalone case (`BOOST_JSON_STANDALONE_SYMBOL_{IMPORT/EXPORT}`)?  
  
Otherwise when linking dynamically and manually setting `BOOST_SYMBOL_IMPORT` and `BOOST_SYMBOL_EXPORT` it works correctly.  
  
> I don't know if Boost.JSON can support building a shared library in standalone. Doing so would require copying a lot of Boost.Config for the various compliers and toolchains  
  
CMake has some functionality to [generate export macros](https://cmake.org/cmake/help/latest/module/GenerateExportHeader.html) tailored for the target platform. Would it be feasible to use that in standalone mode instead of requiring the user to specify the macros?

---

## Comment 5

> Username: vinniefalco  
> Created at: 2020-11-17 19:39:45 UTC  
> Url: https://github.com/boostorg/json/issues/467#issuecomment-729156924  

We can change it so users define `BOOST_JSON_DECL` and `BOOST_JSON_CLASS_DECL` instead, which will be more friendly when also trying to use Boost. Would this be better? However, note that the Jamfile and CMakeList.txt which come with the library are not intended to support every standalone configuration.

---

## Comment 6

> Username: vinniefalco  
> Created at: 2020-11-17 19:48:56 UTC  
> Url: https://github.com/boostorg/json/issues/467#issuecomment-729162161  

How about this change: https://github.com/vinniefalco/json/commit/8f5c5790c93897b78f50b6d2c5efb07495607472  
  
If this change is acceptable, could you put together a patch which sets these macros in the CMakeLists.txt when building standalone and `BUILD_SHARED_LIBS=ON`?

---

## Comment 7

> Username: ennis  
> Created at: 2020-11-18 09:39:26 UTC  
> Url: https://github.com/boostorg/json/issues/467#issuecomment-729557992  

Seems good. I'm not sure how to patch the CMakeLists.txt in a portable way though: `__declspec(dll{export,import})` is only for windows and I'm not familiar with what's needed on other platforms. I usually use either a pre-made header like boost/config.hpp or use generate_export_header which generates one, but this requires changes to the source. I can try to make a PR once I find time.

---

## Comment 8

> Username: vinniefalco  
> Created at: 2020-11-18 13:36:53 UTC  
> Url: https://github.com/boostorg/json/issues/467#issuecomment-729681417  

Hmm I was under the impression that CMake could set the macros (not generate the header). I will leave it at that then, no need to work on a patch (thanks!).
