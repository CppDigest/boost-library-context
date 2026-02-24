# #320 - Removed `boost::filesystem::{filesystem_error,is_regular}` between 1.84 & 1.86? [Closed]

> Username: h-vetinari  
> Created at: 2024-08-17 04:43:48 UTC  
> Updated at: 2024-08-17 12:36:58 UTC  
> Closed at: 2024-08-17 09:05:08 UTC  
> Url: https://github.com/boostorg/filesystem/issues/320  

As part of rebuilding conda-forge for boost 1.86, I'm seeing the following compilation issue for [mongodb](https://github.com/conda-forge/mongodb-feedstock/pull/95) on osx with clang 16 + libcxx 18, as well as on windows with MSVC 19.40 (VS 2022.10):  
```  
[999+4/4035] Compiling build/opt/mongo/db/initialize_server_global_state.o  
FAILED: build/opt/mongo/db/initialize_server_global_state.o   
rm -f build/opt/mongo/db/initialize_server_global_state.o; export PATHOSX='/usr/local/bin:/usr/bin:/bin:/usr/sbin:/sbin:/Library/Apple/usr/bin:/Library/Frameworks/Mono.framework/Versions/Current/Commands';x86_64-apple-darwin13.4.0-clang++ @build/opt/mongo/db/initialize_server_global_state.o.rsp  
src/mongo/db/initialize_server_global_state.cpp:297:14: error: no type named 'filesystem_error' in namespace 'boost::filesystem'; did you mean 'std::filesystem::filesystem_error'?  
    } catch (boost::filesystem::filesystem_error& e) {  
             ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
             std::filesystem::filesystem_error  
$BUILD_PREFIX/bin/../include/c++/v1/__filesystem/filesystem_error.h:31:73: note: 'std::filesystem::filesystem_error' declared here  
class _LIBCPP_AVAILABILITY_FILESYSTEM_LIBRARY _LIBCPP_EXPORTED_FROM_ABI filesystem_error : public system_error {  
                                                                        ^  
src/mongo/db/initialize_server_global_state.cpp:310:65: error: no member named 'is_regular' in namespace 'boost::filesystem'  
        if (!serverGlobalParams.logAppend && boost::filesystem::is_regular(absoluteLogpath)) {  
                                             ~~~~~~~~~~~~~~~~~~~^  
2 errors generated.  
```  
  
That's for mongodb 6.0.16, but upstream mongo still [uses](https://github.com/mongodb/mongo/blob/9fa42b9a4ebcdd2d4c00c7d156d25f4efdbd6974/src/mongo/db/initialize_server_global_state.cpp#L321-L335) `boost::filesystem::{filesystem_error,is_regular}` as of today.  
  
From a quick look around, the only relevant changes I see are https://github.com/boostorg/filesystem/commit/3b55b7b0d35fe8335240d363b4ff7b7abb5a65fe?

---

## Comment 1

> Username: Lastique  
> Created at: 2024-08-17 08:42:06 UTC  
> Url: https://github.com/boostorg/filesystem/issues/320#issuecomment-2294796185  

`is_regular` was indeed removed in 1.85.0, as was described in the [release notes](https://www.boost.org/doc/libs/1_86_0/libs/filesystem/doc/release_history.html). You can replace it with `is_regular_file`.  
  
`filesystem_error` is still present and provided by [`boost/filesystem/exception.hpp`](https://github.com/boostorg/filesystem/blob/41a990ef14760d487c8c3b234f5e3e59178140df/include/boost/filesystem/exception.hpp). I'm not sure why it isn't found in your build. Perhaps, there's a different installation of Boost on your system that gets included?

---

## Comment 2

> Username: Lastique  
> Created at: 2024-08-17 09:05:08 UTC  
> Updated at: 2024-08-17 10:53:54 UTC  
> Url: https://github.com/boostorg/filesystem/issues/320#issuecomment-2294801507  

Actually, the file you referenced from [mongodb 6.0.16](https://github.com/mongodb/mongo/blob/r6.0.16/src/mongo/db/initialize_server_global_state.cpp) is missing `#include <boost/filesystem/exception.hpp>`, which is why `filesystem_error` is not defined. The newer version has that include added.

---

## Comment 3

> Username: h-vetinari  
> Created at: 2024-08-17 09:19:36 UTC  
> Url: https://github.com/boostorg/filesystem/issues/320#issuecomment-2294804887  

OK, thanks for the info. It seems these deprecations were not very visible, because I'm encountering waaaaaay more failures than usual for a simple version bump of boost across a variety of projects.

---

## Comment 4

> Username: Lastique  
> Created at: 2024-08-17 10:59:52 UTC  
> Url: https://github.com/boostorg/filesystem/issues/320#issuecomment-2294826386  

`is_regular` was [marked as deprecated](https://github.com/boostorg/filesystem/blob/e299a19eb51e6ef0e2428acca4a439a07ce0aa99/include/boost/filesystem/operations.hpp#L295) in 1.84.0 and emitted warnings, unless you defined `BOOST_FILESYSTEM_ALLOW_DEPRECATED`, `BOOST_ALLOW_DEPRECATED_SYMBOLS` or `BOOST_ALLOW_DEPRECATED`. Make sure you're not defining one of these macros if you want to be aware of deprecated API usage.

---

## Comment 5

> Username: h-vetinari  
> Created at: 2024-08-17 11:58:46 UTC  
> Updated at: 2024-08-17 12:36:58 UTC  
> Url: https://github.com/boostorg/filesystem/issues/320#issuecomment-2294838498  

I'm not responsible for the respective repos and their testing practices. However, as a distributor, I tend to see ~100s of different repos being rebuilt continuously, and in this case I can report that a lot more is breaking than usual. That's it. 🙃

---

## Comment 6

> Username: Lastique  
> Created at: 2024-08-17 12:09:56 UTC  
> Url: https://github.com/boostorg/filesystem/issues/320#issuecomment-2294841715  

I understand. I'm just saying that on Boost.Filesystem side we are doing whatever we reasonably can to notify and help downstream projects to move away from deprecated APIs. We can only do so much if the downstream projects don't update their code in due time.  
  
If you want to improve the situation, please notify the affected downstream project devs to update their code accordingly, and maybe ask them to remove the macro defines that silence the deprecated warnings to avoid problems like this in the future.
