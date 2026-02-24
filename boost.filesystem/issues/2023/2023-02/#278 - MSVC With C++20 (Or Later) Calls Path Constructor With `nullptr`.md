# #278 - MSVC With C++20 (Or Later) Calls Path Constructor With `nullptr` [Closed]

> Username: ajorians  
> Created at: 2023-02-02 22:04:52 UTC  
> Updated at: 2023-02-04 17:24:52 UTC  
> Closed at: 2023-02-04 17:24:52 UTC  
> Url: https://github.com/boostorg/filesystem/issues/278  

Please consider the following sample code:  
```cpp  
#include <functional>  
#include <boost/filesystem.hpp>  
  
int main()  
{  
   std::function<void(boost::filesystem::path)> myfunc;  
  
   if ( !myfunc )//OK compiles just fine  
      return 0;  
  
   if ( myfunc == nullptr )//Latest MSVC + /std:c++20 this fails to build  
      return 0;  
}  
```  
  
Here is an online link where you can see with Boost `1.80.0` it builds just fine: https://godbolt.org/z/e6zdc8WfM but that is because Compiler Explorer only offers up to version `1.80.0` at the time being.  But if it had version `1.81.0` I claim you would see the error I will describe below.  
  
Using either Visuals Studio 2022 as well as Visual Studio 2019 and compiling as C++20 I get the following build error:  
<details>  
Rebuild started...  
1>------ Rebuild All started: Project: TestBoostFilesystem, Configuration: Debug x64 ------  
1>main.cpp  
1>C:\Users\A.J. Orians\.CommonCppCache\repocache\boost-1.81.0-win\0d44f38a132c865549ff3aee332d2b8e46482759\include\boost\filesystem\detail\path_traits.hpp(495,52): error C2668: 'boost::filesystem::detail::path_traits::is_convertible_to_path_source<nullptr>::_check_convertible_to_path_source': ambiguous call to overloaded function  
1>C:\Users\A.J. Orians\.CommonCppCache\repocache\boost-1.81.0-win\0d44f38a132c865549ff3aee332d2b8e46482759\include\boost\filesystem\detail\path_traits.hpp(481,21): message : could be 'boost::filesystem::detail::path_traits::yes_type boost::filesystem::detail::path_traits::is_convertible_to_path_source<nullptr>::_check_convertible_to_path_source(const wchar_t *)'  
1>C:\Users\A.J. Orians\.CommonCppCache\repocache\boost-1.81.0-win\0d44f38a132c865549ff3aee332d2b8e46482759\include\boost\filesystem\detail\path_traits.hpp(480,21): message : or       'boost::filesystem::detail::path_traits::yes_type boost::filesystem::detail::path_traits::is_convertible_to_path_source<nullptr>::_check_convertible_to_path_source(const char *)'  
1>C:\Users\A.J. Orians\.CommonCppCache\repocache\boost-1.81.0-win\0d44f38a132c865549ff3aee332d2b8e46482759\include\boost\filesystem\detail\path_traits.hpp(495,52): message : while trying to match the argument list '(const T)'  
1>        with  
1>        [  
1>            T=nullptr  
1>        ]  
1>C:\Users\A.J. Orians\.CommonCppCache\repocache\boost-1.81.0-win\0d44f38a132c865549ff3aee332d2b8e46482759\include\boost\type_traits\disjunction.hpp(27,1): message : see reference to class template instantiation 'boost::filesystem::detail::path_traits::is_convertible_to_path_source<nullptr>' being compiled  
1>C:\Users\A.J. Orians\.CommonCppCache\repocache\boost-1.81.0-win\0d44f38a132c865549ff3aee332d2b8e46482759\include\boost\type_traits\disjunction.hpp(31,1): message : see reference to class template instantiation 'boost::disjunction<boost::filesystem::detail::path_traits::is_convertible_to_path_source<nullptr>>' being compiled  
1>C:\Users\A.J. Orians\.CommonCppCache\repocache\boost-1.81.0-win\0d44f38a132c865549ff3aee332d2b8e46482759\include\boost\type_traits\conjunction.hpp(31,1): message : see reference to class template instantiation 'boost::disjunction<boost::filesystem::detail::path_traits::is_path_source<nullptr>,boost::filesystem::detail::path_traits::is_convertible_to_path_source<nullptr>>' being compiled  
1>C:\Users\A.J. Orians\.CommonCppCache\repocache\boost-1.81.0-win\0d44f38a132c865549ff3aee332d2b8e46482759\include\boost\filesystem\path.hpp(264,27): message : see reference to class template instantiation 'boost::conjunction<boost::disjunction<boost::filesystem::detail::path_traits::is_path_source<nullptr>,boost::filesystem::detail::path_traits::is_convertible_to_path_source<nullptr>>,boost::negation<boost::filesystem::detail::path_traits::is_native_path_source<nullptr>>>' being compiled  
1>Done building project "TestBoostFilesystem.vcxproj" -- FAILED.  
========== Rebuild All: 0 succeeded, 1 failed, 0 skipped ==========  
========== Elapsed 00:03.205 ==========  
</details>  
  
![C++20PathBuildError](https://user-images.githubusercontent.com/3475163/216456451-b81c4683-fb48-46f3-8525-a00651b14ff1.png)  
  
That error is pretty much the exact same output as when I write:  
```cpp  
boost::filesystem::path p( nullptr );  
```  
  
I am inclined to believe this is a MSVC bug because I can use Boost `1.81.0` with both GCC & Clang on Compiler Explorer to which they both build in C++20 with that sample code.  Here is an online link: https://godbolt.org/z/f1jzWM6or  That link also has the extra line to try to which you can see a similar build error with Boost `1.81.0` that you don't see with Boost `1.80.0`.  
  
So I do have a workaround as well as perhaps an updated MSVC could fix this.  So just so you are aware.  I didn't see any compile option/preprocessor definition I could set to workaround my problem.  
  
Thanks for considering!

---

## Comment 1

> Username: Lastique  
> Created at: 2023-02-03 00:36:40 UTC  
> Url: https://github.com/boostorg/filesystem/issues/278#issuecomment-1414554962  

1. Constructing `path` from `nullptr` is always UB and never correct. The fact that it fails to compile is a good thing, although the error could be better.  
2. As to why the compiler tries to construct `path` from `nullptr` when you compare it with `std::function`, I really have no idea. This looks like a compiler issue to me, you should report it to MS.

---

## Comment 2

> Username: ajorians  
> Created at: 2023-02-03 13:08:02 UTC  
> Updated at: 2023-02-03 13:27:54 UTC  
> Url: https://github.com/boostorg/filesystem/issues/278#issuecomment-1415848268  

> 2\. As to why the compiler tries to construct `path` from `nullptr` when you compare it with `std::function`, I really have no idea. This looks like a compiler issue to me, you should report it to MS.  
  
Thank you.  This is more my issue and I'll report it to MS.  I figured I would first mention here because if my `std::function` had a `std::filesystem::path` instead of the `boost` version it does build on MSVC with C++20.  I'll link to the MS issue after I create it (edit: Here is the link: https://developercommunity.visualstudio.com/t/C20-And-Later-Calls-Boost-Path-Con/10270439?port=1025&fsid=e5b4246a-ea5e-466d-95fb-745d83dd8a0a&sort=newest&ref=native&refTime=1675430809006&refUserId=0e05bc7b-454a-461d-bc0b-3ea1e4b3e10c ).  
  
Thanks again!
