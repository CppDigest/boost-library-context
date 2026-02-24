# #921 - Building boost with MSVC fails [Closed]

> Username: FTamas77  
> Created at: 2024-06-13 18:38:53 UTC  
> Updated at: 2024-06-14 16:47:19 UTC  
> Closed at: 2024-06-14 16:47:06 UTC  
> Url: https://github.com/boostorg/boost/issues/921  

### I am using the following setup:  
  
**Visual Studio:**  
  
- Microsoft Visual Studio Professional 2022 (64-bit) - Current Version 17.10.2  
  
**Conan:**  
  
- Conan version 2.4.1  
  
### Here is my Conan profile:  
  
```  
[settings]  
arch=x86_64  
build_type=Debug  
compiler=msvc  
compiler.cppstd=17  
compiler.runtime=dynamic  
compiler.version=194  
os=Windows  
  
[conf]  
tools.microsoft.msbuild:vs_version=17  
```  
  
**When I try to build Boost using Conan with the command:**  
  
`conan install --requires=boost/1.85.0 --build=missing -v`  
  
**I encounter the following error:**  
  
```  
boost/1.85.0: ERROR: Package 'dcbafcaa8ac5749d5d581be6c049bc3da7c6afbe' build failed  
boost/1.85.0: WARN: Build folder D:\conan_data\b\boost53872d69de0c4\b\build-debug  
ERROR: boost/1.85.0: Error in build() method, line 1130  
        self.run(full_command)  
        ConanException: Error 1 while executing  
```  
  
If I try  
`  
conan install --requires=gtest/1.14.0 --build=missing -v`  
  
it works.

---

## Comment 1

> Username: FTamas77  
> Created at: 2024-06-14 16:47:06 UTC  
> Updated at: 2024-06-14 16:47:19 UTC  
> Url: https://github.com/boostorg/boost/issues/921#issuecomment-2168411978  

[It is known problem](https://github.com/conan-io/conan/issues/16465)
