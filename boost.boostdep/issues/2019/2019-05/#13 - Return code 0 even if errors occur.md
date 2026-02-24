# #13 - Return code 0 even if errors occur [Closed]

> Username: jeking3  
> Created at: 2019-05-14 13:24:12 UTC  
> Updated at: 2020-03-11 10:56:35 UTC  
> Closed at: 2020-03-10 13:43:57 UTC  
> Url: https://github.com/boostorg/boostdep/issues/13  

I saw this in a build on Appveyor this morning:  
  
https://ci.appveyor.com/project/jeking3/uuid/builds/24529878/job/rwc2r69ccmy50yii#L39  
  
```  
C:\projects\boost-root>python tools/boostdep/depinst/depinst.py --include benchmark --include example --include examples --include tools  uuid   || EXIT /B   
fatal: unable to access 'https://github.com/boostorg/container_hash.git/': Failed to connect to github.com port 443: Timed out  
fatal: clone of 'https://github.com/boostorg/container_hash.git' into submodule path 'C:/projects/boost-root/libs/container_hash' failed  
Failed to clone 'libs/container_hash'. Retry scheduled  
fatal: unable to access 'https://github.com/boostorg/core.git/': Failed to connect to github.com port 443: Timed out  
fatal: clone of 'https://github.com/boostorg/core.git' into submodule path 'C:/projects/boost-root/libs/core' failed  
Failed to clone 'libs/core'. Retry scheduled  
fatal: unable to access 'https://github.com/boostorg/lexical_cast.git/': Failed to connect to github.com port 443: Timed out  
fatal: clone of 'https://github.com/boostorg/lexical_cast.git' into submodule path 'C:/projects/boost-root/libs/lexical_cast' failed  
Failed to clone 'libs/lexical_cast'. Retry scheduled  
fatal: unable to access 'https://github.com/boostorg/move.git/': Failed to connect to github.com port 443: Timed out  
fatal: clone of 'https://github.com/boostorg/move.git' into submodule path 'C:/projects/boost-root/libs/move' failed  
Failed to clone 'libs/move'. Retry scheduled  
fatal: unable to access 'https://github.com/boostorg/numeric_conversion.git/': Failed to connect to github.com port 443: Timed out  
fatal: clone of 'https://github.com/boostorg/numeric_conversion.git' into submodule path 'C:/projects/boost-root/libs/numeric/conversion' failed  
Failed to clone 'libs/numeric/conversion'. Retry scheduled  
fatal: unable to access 'https://github.com/boostorg/mpl.git/': Failed to connect to github.com port 443: Timed out  
fatal: clone of 'https://github.com/boostorg/mpl.git' into submodule path 'C:/projects/boost-root/libs/mpl' failed  
Failed to clone 'libs/mpl'. Retry scheduled  
fatal: unable to access 'https://github.com/boostorg/preprocessor.git/': Could not resolve host: github.com  
fatal: clone of 'https://github.com/boostorg/preprocessor.git' into submodule path 'C:/projects/boost-root/libs/preprocessor' failed  
Failed to clone 'libs/preprocessor'. Retry scheduled  
fatal: unable to access 'https://github.com/boostorg/range.git/': Could not resolve host: github.com  
fatal: clone of 'https://github.com/boostorg/range.git' into submodule path 'C:/projects/boost-root/libs/range' failed  
Failed to clone 'libs/range'. Retry scheduled  
fatal: unable to access 'https://github.com/boostorg/smart_ptr.git/': Could not resolve host: github.com  
fatal: clone of 'https://github.com/boostorg/smart_ptr.git' into submodule path 'C:/projects/boost-root/libs/smart_ptr' failed  
Failed to clone 'libs/smart_ptr'. Retry scheduled  
fatal: unable to access 'https://github.com/boostorg/spirit.git/': Could not resolve host: github.com  
fatal: clone of 'https://github.com/boostorg/spirit.git' into submodule path 'C:/projects/boost-root/libs/spirit' failed  
Failed to clone 'libs/spirit'. Retry scheduled  
fatal: unable to access 'https://github.com/boostorg/system.git/': Could not resolve host: github.com  
fatal: clone of 'https://github.com/boostorg/system.git' into submodule path 'C:/projects/boost-root/libs/system' failed  
Failed to clone 'libs/system'. Retry scheduled  
fatal: unable to access 'https://github.com/boostorg/unordered.git/': Could not resolve host: github.com  
fatal: clone of 'https://github.com/boostorg/unordered.git' into submodule path 'C:/projects/boost-root/libs/unordered' failed  
Failed to clone 'libs/unordered'. Retry scheduled  
fatal: unable to access 'https://github.com/boostorg/utility.git/': Could not resolve host: github.com  
fatal: clone of 'https://github.com/boostorg/utility.git' into submodule path 'C:/projects/boost-root/libs/utility' failed  
Failed to clone 'libs/utility'. Retry scheduled  
fatal: unable to access 'https://github.com/boostorg/variant.git/': Could not resolve host: github.com  
fatal: clone of 'https://github.com/boostorg/variant.git' into submodule path 'C:/projects/boost-root/libs/variant' failed  
Failed to clone 'libs/variant'. Retry scheduled  
fatal: unable to access 'https://github.com/boostorg/mpl.git/': Could not resolve host: github.com  
fatal: clone of 'https://github.com/boostorg/mpl.git' into submodule path 'C:/projects/boost-root/libs/mpl' failed  
Failed to clone 'libs/mpl' a second time, aborting  
Installing: config, headers, ../tools/boost_install, ../tools/build  
Installing: core, static_assert, random, io, winapi, predef, move, timer, assert, tti, type_traits, lexical_cast, container_hash, test, serialization, array, config, throw_exception, numeric/conversion  
Installing: detail, integer, iterator, utility, multiprecision, container, system, dynamic_bitset, smart_ptr, function, mpl, variant, conversion, preprocessor, optional, spirit, function_types, exception, algorithm, chrono, bind, range, math, unordered  
C:\projects\boost-root>REM Bootstrap is not expecting cxxflags content so we zero it out for the bootstrap only   
C:\projects\boost-root>SET OLD_CXXFLAGS=   
```  
  
The command I run uses `` || EXIT /B `` so that it will exit the batch script if it returns anything non-zero, but in this case it looks like even though github could not be reached, depinst returned 0 indicating success.

---

## Comment 1

> Username: Lastique  
> Created at: 2020-03-09 23:46:34 UTC  
> Url: https://github.com/boostorg/boostdep/issues/13#issuecomment-596831718  

Also [happened](https://travis-ci.org/boostorg/atomic/jobs/660301961#L602) to my build. `depinst` should return an error code if it fails to complete its operations.

---

## Comment 2

> Username: Lastique  
> Created at: 2020-03-11 10:56:35 UTC  
> Url: https://github.com/boostorg/boostdep/issues/13#issuecomment-597567229  

Thanks, Peter.
