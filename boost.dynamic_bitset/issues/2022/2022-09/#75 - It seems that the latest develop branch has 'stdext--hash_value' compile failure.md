# #75 - It seems that the latest develop branch has 'stdext::hash_value' compile failure [Closed]

> Username: WadeGao  
> Created at: 2022-09-13 09:22:52 UTC  
> Updated at: 2024-11-26 21:02:04 UTC  
> Closed at: 2024-11-26 21:02:02 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/issues/75  

I found that the latest develop branch(commit id: 8e20aa1) will generate compile failures because of std::hash related error when using below CI options on Appveyor:  
  
- Environment: FLAVOR=Visual Studio 2017 C++2a Strict, APPVEYOR_BUILD_WORKER_IMAGE=Visual Studio 2017, B2_CXXFLAGS=-permissive-, B2_CXXSTD=2a, B2_TOOLSET=msvc-14.1  
- Environment: FLAVOR=Visual Studio 2017 C++14/17, APPVEYOR_BUILD_WORKER_IMAGE=Visual Studio 2017, B2_CXXSTD=14,17, B2_TOOLSET=msvc-14.1  
- Environment: FLAVOR=clang-cl, APPVEYOR_BUILD_WORKER_IMAGE=Visual Studio 2017, B2_ADDRESS_MODEL=64, B2_CXXSTD=11,14,17, B2_TOOLSET=clang-win  
- Environment: FLAVOR=Visual Studio 2015, 2013, APPVEYOR_BUILD_WORKER_IMAGE=Visual Studio 2015, B2_TOOLSET=msvc-12.0,msvc-14.0  
- Environment: FLAVOR=Visual Studio 2008, 2010, 2012, APPVEYOR_BUILD_WORKER_IMAGE=Visual Studio 2015, B2_TOOLSET=msvc-9.0,msvc-10.0,msvc-11.0, B2_ADDRESS_MODEL=32  
  
To regenerate this issues, I create a redundant draft pull request by adding only single line comment (sorry for my unruly behavior), but the failures still occur, details can be found in [this links](https://ci.appveyor.com/project/jeking3/dynamic-bitset-jv17p/builds/44759480/job/30g481s7rmpqrfy7).  
  
The same failure has affected several recent pull requests  
  
Failure digest:   
```bash  
.\boost/container_hash/hash.hpp(669): error C2668: 'stdext::hash_value': ambiguous call to overloaded function  
C:\Program Files (x86)\Microsoft Visual Studio\2017\Community\VC\Tools\MSVC\14.16.27023\include\xhash(29): note: could be 'size_t stdext::hash_value<T>(const _Kty &)' [found using argument-dependent lookup]  
        with  
        [  
            T=std::vector<unsigned long,std::allocator<unsigned long>>,  
            _Kty=std::vector<unsigned long,std::allocator<unsigned long>>  
        ]  
.\boost/container_hash/hash.hpp(406): note: or       'unsigned int boost::hash_value<T>(const T &)'  
        with  
        [  
            T=std::vector<unsigned long,std::allocator<unsigned long>>  
        ]  
.\boost/container_hash/hash.hpp(669): note: while trying to match the argument list '(const T)'  
        with  
        [  
            T=std::vector<unsigned long,std::allocator<unsigned long>>  
        ]  
```

---

## Comment 1

> Username: jeking3  
> Created at: 2024-11-26 21:02:02 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/issues/75#issuecomment-2501928231  

Given CI is passing on some of those without incident, this was an issue in container_hash and not in dynamic_bitset.  Closing this.
