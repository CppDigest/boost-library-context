# #52 - Failing to use leaf with Conan and CMake [Closed]

> Username: up2datecyborg  
> Created at: 2022-12-19 10:40:17 UTC  
> Updated at: 2023-01-09 17:07:51 UTC  
> Closed at: 2023-01-09 17:07:51 UTC  
> Url: https://github.com/boostorg/leaf/issues/52  

Hi,  
  
I used for a year stx library, and wanted to give boost::leaf a try.  
As of writing the latest version of boost in Conan is 1.80.0.  
As the conan package boost-leaf (1.81.0) was marked as deprecated recently, I cannot use leaf with Conan and CMake.  
It seems CMake component leaf is inexistent.  
  
In my conanfile.txt I have:  
`[requires]  
boost/1.80.0`  
  
In my CMakeLists.txt:  
`find_package(Boost REQUIRED COMPONENTS leaf)`  
  
It's failing with:  
`Conan: Component 'leaf' NOT found in package 'Boost'`  
  
Looking at the files in ~/.conan/, the library is there, but there is no reference to it in the conan/cmake files.  
  
Am I doing something wrong?

---

## Comment 1

> Username: kammce  
> Created at: 2022-12-20 00:28:01 UTC  
> Url: https://github.com/boostorg/leaf/issues/52#issuecomment-1358674964  

I tested this out and you should do the following:  
  
## conanfile.txt  
```  
[requires]  
boost/1.80.0  
// other dependencies  
  
[generators]  
CMakeToolchain  
CMakeDeps  
VirtualRunEnv  
```  
  
## `CMakeLists.txt`  
### Find package using config  
  
```  
find_package(Boost REQUIRED CONFIG)  
```  
  
### Link project  
```  
target_link_libraries(${PROJECT_NAME} PRIVATE Boost::boost)  
```

---

## Comment 2

> Username: up2datecyborg  
> Created at: 2022-12-20 11:30:00 UTC  
> Url: https://github.com/boostorg/leaf/issues/52#issuecomment-1359217233  

Thanks, I'll try it, I guess there is no component because it's a header-only lib.

---

## Comment 3

> Username: kammce  
> Created at: 2022-12-20 13:55:22 UTC  
> Url: https://github.com/boostorg/leaf/issues/52#issuecomment-1359395512  

Yeah I'm not sure why they didn't make it into its own component. From the looks of it, only a few items within the boost library are pulled out to their own component.

---

## Comment 4

> Username: up2datecyborg  
> Created at: 2023-01-09 17:07:51 UTC  
> Url: https://github.com/boostorg/leaf/issues/52#issuecomment-1375963256  

Sorry for the delay, what you explained is working fine.  
Thanks!
