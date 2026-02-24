# #195 - Expose Boost.Stacktrace Internal Libraries as Optional in Boost.Build [Closed]

> Username: uilianries  
> Created at: 2024-11-21 08:59:29 UTC  
> Updated at: 2025-01-18 10:26:23 UTC  
> Closed at: 2025-01-18 10:26:23 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/195  

### Background  
  
The recent commit [22982db](https://github.com/boostorg/stacktrace/commit/22982db3f6630de16434150d181e0da07ce76d74) in Boost.Stacktrace's master branch shows a discrepancy between CMake and Boost.Build configuration options.  
  
## Current Situation  
  
#### CMake Support  
  
The [CMakeLists.txt](https://github.com/boostorg/stacktrace/blob/22982db3f6630de16434150d181e0da07ce76d74/CMakeLists.txt#L79) provides granular control over each supported library in the project:  
  
* Users can decide which internal libraries to build when available  
* Options are clearly exposed and configurable  
* Dependencies are properly handled  
  
#### Boost.Build Support  
  
Currently, Boost.Build only exposes `stacktrace_from_exception` as a configurable feature:  
  
* [boost-stacktrace-features.jam](https://github.com/boostorg/stacktrace/blob/22982db3f6630de16434150d181e0da07ce76d74/boost-stacktrace-features.jam)  
*  [build/Jamfile.v2](https://github.com/boostorg/stacktrace/blob/22982db3f6630de16434150d181e0da07ce76d74/build/Jamfile.v2)  
  
### Problem  
  
While CMake configuration provides flexibility, we must use Boost.Build as it's the [official build system](https://github.com/boostorg/cmake?tab=readme-ov-file#boost-cmake-support-infrastructure) for Boost. This creates challenges for package managers like Conan.  
Impact  
  
At [ConanCenterIndex](https://github.com/conan-io/conan-center-index/blob/master/recipes/boost/all/conanfile.py), we maintain Boost packages and need to:  
  
* Implement additional logic to handle stacktrace's internal libraries  
* Create workarounds since libraries may or may not be built  
* Patch code from the package manager side due to lack of configuration options, to know if a backtrace library will be built or not  
  
### Request  
  
Please consider exposing the internal libraries of Boost.Stacktrace as optional features in Boost.Build, similar to the CMake implementation. This would:  
  
* Provide consistent configuration options across build systems  
* Reduce the need for package manager workarounds  
* Improve user control over the build process  
  
### Additional Context  
  
This enhancement would particularly benefit package managers and users who need fine-grained control over which stacktrace components are built.

---

## Comment 1

> Username: apolukhin  
> Created at: 2024-12-22 13:37:21 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/195#issuecomment-2558459845  

I'd appreciate a PR
