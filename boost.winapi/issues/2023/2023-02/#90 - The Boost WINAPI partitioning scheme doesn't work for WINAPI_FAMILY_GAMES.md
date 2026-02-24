# #90 - The Boost WINAPI partitioning scheme doesn't work for WINAPI_FAMILY_GAMES [Open]

> Username: walbourn  
> Created at: 2023-02-18 08:38:31 UTC  
> Updated at: 2025-11-18 21:10:33 UTC  
> Url: https://github.com/boostorg/winapi/issues/90  

For Xbox development, we make use of ``WINAPI_FAMILY_GAMES`` which is supported by the Windows 10 SDK (19041) or later.  
  
When you build with ``WINAPI_FAMILY=WINAPI_FAMILY_GAMES``, boost ends up misconfiguring a number of APIs which are supported such as ``SwitchToThread``. Part of the problem is that ``WINAPI_FAMILY_GAMES`` by design does NOT include everything in WINAPI_PARTITION_SYSTEM. ``WINAPI_FAMILY_GAMES`` contains many more Win32 APIs than UWP, but less than the full DESKTOP partition.  
  
The good news is that most the Win32 APIs used by the **winapi** headers ARE supported for ``WINAPI_FAMILY_GAMES``.  
  
The only APIs that are NOT in WINAPI_FAMILY_GAMES are:  
  
* The WinCrypt functions in crypt.hpp (bcrypt.hpp is supported instead).  
* The dbghelp.hpp functions  
* ``FlushViewOfFile``  
* ``CompareObjectHandles``  
* Jobs APIs in jobs.hpp  
* ``ImpersonateNamedPipeClient``. ``TransactNamedPipe``. ``GetNamedPipeClientComputerNameA``, ``GetNamedPipeClientComputerNameW``  
*  The priority class APIs in priority_class.hpp  
* The security object APIs in security.hpp  
* Shell APIs in shell.hpp  
* ``ShowWindowAsync``  
  
Fixing this is important for making the rest of boost work on the Xbox platform.  
  
> Note that for Xbox development, we officially support the MSVC VS 2017 (v141) 15.9 or later toolset as well as clang/LLVM for Windows v11 or later. Due to some link warnings with newer SDKs and Microsoft GDKs, clang/LLVM for Windows v15 or later is recommended. We also recommend VS 2019 (v142) 16.11 or later.  
  
> There's no need to access the NDA Microsoft GDK with Xbox Extensions for this work as all the relevant APIs are in the public Windows SDK (19041) or later.

---

## Comment 1

> Username: walbourn  
> Created at: 2023-02-18 20:43:17 UTC  
> Updated at: 2023-02-19 04:39:55 UTC  
> Url: https://github.com/boostorg/winapi/issues/90#issuecomment-1435765294  

From an initial test, it appears the following boost modules build fine with ``WINAPI_FAMILY_GAMES`` with things as they are.  
  
```  
boost-algorithm  
boost-align  
boost-any  
boost-array  
boost-assert  
boost-bind  
boost-build  
boost-callable-traits  
boost-circular-buffer  
boost-compatibility  
boost-concept-check  
boost-config  
boost-container  
boost-container-hash  
boost-context  
boost-conversion  
boost-core  
boost-coroutine2  
boost-crc  
boost-date-time  
boost-describe  
boost-detail  
boost-dynamic-bitset  
boost-endian  
boost-exception  
boost-foreach  
boost-format  
boost-function  
boost-function-types  
boost-functional  
boost-fusion  
boost-gil  
boost-hana  
boost-heap  
boost-hof  
boost-icl  
boost-integer  
boost-interval  
boost-intrusive  
boost-io  
boost-iterator  
boost-lambda  
boost-lambda2  
boost-leaf  
boost-lexical-cast  
boost-local-function  
boost-logic  
boost-metaparse  
boost-modular-build-helper  
boost-move  
boost-mp11  
boost-mpl  
boost-multi-array  
boost-numeric-conversion  
boost-optional  
boost-outcome  
boost-parameter  
boost-pfr  
boost-phoenix  
boost-polygon  
boost-pool  
boost-predef  
boost-preprocessor  
boost-program-options  
boost-property-map  
boost-proto  
boost-qvm  
boost-range  
boost-ratio  
boost-rational  
boost-regex  
boost-safe-numerics  
boost-scope-exit  
boost-signals2  
boost-smart-ptr  
boost-sort  
boost-static-assert  
boost-static-string  
boost-stl-interfaces  
boost-system  
boost-throw-exception  
boost-tokenizer  
boost-tti  
boost-tuple  
boost-type-index  
boost-type-traits  
boost-typeof  
boost-uninstall  
boost-unordered  
boost-utility  
boost-variant  
boost-variant2  
boost-vmd  
boost-winapi  
boost-yap  
```  
  
The following are currently blocked by the **winapi** behavior:  
  
```  
boost-accumulators  
boost-asio  
boost-assign  
boost-atomic  
boost-beast  
boost-bimap  
boost-chrono  
boost-compute  
boost-contract  
boost-convert  
boost-dll  
boost-fiber  
boost-filesystem  
boost-flyweight  
boost-geometry  
boost-graph  
boost-graph-parallel  
boost-histogram  
boost-iostreams  
boost-json  
boost-locale  
boost-lockfree  
boost-log  
boost-math  
boost-mpi  
boost-msm  
boost-multi-index  
boost-multiprecision  
boost-nowide  
boost-odeint  
boost-parameter-python  
boost-poly-collection  
boost-property-map-parallel  
boost-property-tree  
boost-ptr-container  
boost-python  
boost-random  
boost-serialization  
boost-spirit  
boost-statechart  
boost-thread  
boost-timer  
boost-type-erasure  
boost-ublas  
boost-units  
boost-url  
boost-uuid  
boost-wave  
boost-xpressive  
```  
  
The following libs will need some modifications for the fact that some underlying APIs are not supported for ``WINAPI_FAMILY_GAMES``:  
  
```  
boost-interprocess  
boost-process  
boost-stacktrace  
```  
  
Of the list of failing libraries, the fix for **boost-atomic** is critical as that is used by many other libs, and both the 'missing' APIs are fully supported for ``WINAPI_FAMILY_GAMES``: ``SwitchToThread`` and ``Sleep``.  
  
The other main blocker is ``FormatMessage`` and associated use of ``LocalFree`` which are both supported by the ``WINAPI_FAMILY_GAMES`` partition, but since its "missing" many fundamental ports fail to build including **boost-chrono**, **boost-coroutine**, **boost-json**, **boost-random**, and **boost-url**.  
  
> Note that ``boost-test`` won't run on Xbox itself since that platform doesn't support "Console" APIs, but in theory these could just use OutputDebugString instead.

---

## Comment 2

> Username: walbourn  
> Created at: 2023-03-01 20:24:26 UTC  
> Url: https://github.com/boostorg/winapi/issues/90#issuecomment-1450799523  

Note this is related to [this work on vcpkg](https://github.com/microsoft/vcpkg/pull/29210).

---

## Comment 3

> Username: whyjp  
> Created at: 2023-04-26 10:08:40 UTC  
> Url: https://github.com/boostorg/winapi/issues/90#issuecomment-1523156765  

hello  
I'm going to port a library that needs to be included in game development for xbox.  
In the meantime, I need to be able to build and use boost, but I couldn't find a way.  
It's not easy to find information about xbox gdk or sony playstation development  
Our library includes boost/openssl, etc.  
Among them, asio is the most important function  
Because it supports multi-platform  
  
Then I found this thread for boost build for xbox.  
  
For Xbox development, we officially support the MSVC VS 2017 (v141) 15.9+ toolset and clang/LLVM for Windows v11+. clang/LLVM is recommended for Windows v15 and later due to some link warnings in the latest SDK and Microsoft GDK. We also recommend VS 2019 (v142) 16.11 or newer.  
  
You don't need access to the NDA Microsoft GDK with Xbox extensions for this to work, as all relevant APIs are in the public Windows SDK (19041) or higher.  
  
Does this comment mean you can build boost against the xbox gdk?  
  
Solution b2 build option not found for gdk....  
Can you help me out with a small comment?

---

## Comment 4

> Username: walbourn  
> Created at: 2023-04-26 17:53:03 UTC  
> Url: https://github.com/boostorg/winapi/issues/90#issuecomment-1523826912  

I added triplets to build for GDK scenarios using VCPKG.  
  
https://walbourn.github.io/vcpkg-now-supports-targeting-xbox/  
  
This issue is that the partitioning isn't quite right for WINAPI_FAMILY_GAMES, so about half the boost libraries build right now. If we can get this issue fixed, then it should be able to build the whole library.

---

## Comment 5

> Username: walbourn  
> Created at: 2025-11-18 21:10:33 UTC  
> Url: https://github.com/boostorg/winapi/issues/90#issuecomment-3549527380  

Note that Windows SDK (22000) is now the minimum supported version used by Xbox One and Xbox Series X\|S development as we dropped support for older SDKs.  
  
I'm still hitting failures with the following when building for `WINAPI_FAMILY_GAMES`.  
  
|Boost module|Failure|  
|---|---|  
|boost-atomic|SwitchToThread is not defined, but is supported|  
|boost-chrono|FORMAT_MESSAGE_ALLOCATE_BUFFER_ and LocalFree are not defined, but is supported|  
|boost-coroutine|^|  
|boost-json|^|  
|boost-random|^|  
|boost-url|^|  
|boost-stacktrace|RtlCaptureStackBackTrace,VirtualQuery,GetModuleFileNameA are missing but are supported|
