# #1159 - compile error: expected primary-expression before... [Closed]

> Username: tanghong95  
> Created at: 2023-06-07 03:17:28 UTC  
> Updated at: 2023-06-08 07:55:53 UTC  
> Closed at: 2023-06-08 07:55:53 UTC  
> Url: https://github.com/boostorg/geometry/issues/1159  

**Describe the bug**  
when I use the boost/geometry installed via vcpkg, the g++ report this error: "expected primary-expression before..."  
  
**Environment**  
- OS: Ubuntu 20.04.5 LTS  
- Compiler: GNU 9.4.0 and GNU 10.3.0  
  
**Failure logs**  
```  
...  
  
[build] In file included from /data/vcpkg/installed/x64-linux/include/boost/geometry/srs/projections/proj/aea.hpp:61,  
[build]                  from /data/vcpkg/installed/x64-linux/include/boost/geometry/srs/projections/factory.hpp:27,  
[build]                  from /data/vcpkg/installed/x64-linux/include/boost/geometry/srs/projection.hpp:31,  
[build]                  from /data/vcpkg/installed/x64-linux/include/boost/geometry/srs/epsg.hpp:14,  
[build]                  from /A.h:19,  
[build]                  from /B.h:2,  
[build]                  from /C.h:4,  
[build]                  from /D.cpp:2:  
[build] /data/vcpkg/installed/x64-linux/include/boost/geometry/srs/projections/impl/pj_qsfn.hpp: In function ‘T boost::geometry::projections::detail::pj_qsfn(const T&, const T&, const T&)’:  
[build] /data/vcpkg/installed/x64-linux/include/boost/geometry/srs/projections/impl/pj_qsfn.hpp:58:27: error: expected primary-expression before ‘(’ token  
[build]    58 |            (.5 / e) * log ((1. - con) / (1. + con))));  
[build]       |                           ^  
[build] In file included from /data/vcpkg/installed/x64-linux/include/boost/geometry/srs/projections/factory.hpp:27,  
[build]                  from /data/vcpkg/installed/x64-linux/include/boost/geometry/srs/projection.hpp:31,  
[build]                  from /data/vcpkg/installed/x64-linux/include/boost/geometry/srs/epsg.hpp:14,  
[build]                  from /A.h:19,  
[build]                  from /B.h:2,  
[build]                  from /C.h:4,  
[build]                  from /Dcpp:2:  
[build] /data/vcpkg/installed/x64-linux/include/boost/geometry/srs/projections/proj/aea.hpp: In function ‘T boost::geometry::projections::detail::aea::phi1_(const T&, const T&, const T&)’:  
[build] /data/vcpkg/installed/x64-linux/include/boost/geometry/srs/projections/proj/aea.hpp:111:52: error: expected primary-expression before ‘(’ token  
[build]   111 |                        sinpi / com + .5 / Te * log ((1. - con) /  
[build]       |                                                    ^  
[build] In file included from /data/vcpkg/installed/x64-linux/include/boost/geometry/srs/projections/factory.hpp:27,  
[build]                  from /data/vcpkg/installed/x64-linux/include/boost/geometry/srs/projection.hpp:31,  
[build]                  from /data/vcpkg/installed/x64-linux/include/boost/geometry/srs/epsg.hpp:14,  
  
...  
  
[build] ninja: build stopped: subcommand failed.  
[proc] The command: /snap/bin/cmake --build /project/build/RelWithDebInfo --config RelWithDebInfo --target all -j 4 -- exited with code: 1  
[driver] Build completed: 00:00:50.925  
[build] Build finished with exit code 1  
```

---

## Comment 1

> Username: tanghong95  
> Created at: 2023-06-08 07:55:53 UTC  
> Url: https://github.com/boostorg/geometry/issues/1159#issuecomment-1582082464  

This question was caused by the compiler version and I will close this issue.
