# #78 - unresolved external symbol BCrypt*** with ninja and clang-cl [Closed]

> Username: Abergard  
> Created at: 2018-08-18 18:11:27 UTC  
> Updated at: 2019-06-09 17:03:39 UTC  
> Closed at: 2019-06-09 17:03:39 UTC  
> Url: https://github.com/boostorg/uuid/issues/78  

Hi,  
  
today  I have prepared upgarde boost to 1.68 and I can't compile my project. I have prepared test program to reproduce issue:  
  
CMakeLists.txt:  
```  
cmake_minimum_required(VERSION 3.10)  
project("only4test" CXX)  
  
find_package(Boost 1.6 REQUIRED)  
  
add_executable(only4test  
  main.cpp  
  )  
  
target_compile_options(only4test  
  PRIVATE /W4)  
  
target_link_libraries(only4test  
  PRIVATE Boost::boost  
  )  
```  
  
main.cpp  
```  
#include <iostream>  
#include <boost/uuid/uuid_generators.hpp>  
#include <boost/uuid/uuid_io.hpp>  
  
using namespace std;  
  
int main()  
{  
    cout << boost::uuids::to_string(boost::uuids::random_generator()()) << '\n';  
}  
```  
  
When it is compile with msvc it works, however when I change compiler to clang-cl the bcrypt library is not automaticly linked.  
  
Msvc compilation:  
```  
cmake -G "Visual Studio 15 2017 Win64" .. -DBOOST_ROOT=%SRC%/boost_1_68_0  
cmake --build .  
Build succeeded.  
    0 Warning(s)  
    0 Error(s)  
```  
  
Clang compilation:  
```  
cmake -G "Ninja" .. -DBOOST_ROOT=%SRC%/boost_1_68_0/ -DCMAKE_CXX_COMPILER=clang-cl -DCMAKE_C_COMPILER=clang-cl  
cmake --build .  
main.cpp.obj : error LNK2019: unresolved external symbol BCryptOpenAlgorithmProvider referenced in function "public: __cdecl boost::uuids::detail::random_provider_base::random_provider_base(void)" (??0random_provider_base@detail@uuids@boost@@QEAA@XZ)  
main.cpp.obj : error LNK2019: unresolved external symbol BCryptGenRandom referenced in function "public: void __cdecl boost::uuids::detail::random_provider_base::get_random_bytes(void *,unsigned __int64)" (?get_random_bytes@random_provider_base@detail@uuids@boost@@QEAAXPEAX_K@Z)  
main.cpp.obj : error LNK2019: unresolved external symbol BCryptCloseAlgorithmProvider referenced in function "private: void __cdecl boost::uuids::detail::random_provider_base::destroy(void)" (?destroy@random_provider_base@detail@uuids@boost@@AEAAXXZ)  
only4test.exe : fatal error LNK1120: 3 unresolved externals  
ninja: build stopped: subcommand failed.  
```  
  
Did I need some additional flag to have auto link worked?

---

## Comment 1

> Username: Lastique  
> Created at: 2018-08-18 18:31:36 UTC  
> Url: https://github.com/boostorg/uuid/issues/78#issuecomment-414077782  

Yes, see https://github.com/boostorg/uuid/issues/68#issuecomment-387061372.  
  
I don't know if clang-cl supports auto-linking, but if it does consider creating a pull request for https://github.com/boostorg/config/blob/develop/include/boost/config/auto_link.hpp.

---

## Comment 2

> Username: jeking3  
> Created at: 2018-08-19 14:38:41 UTC  
> Updated at: 2018-08-19 14:41:43 UTC  
> Url: https://github.com/boostorg/uuid/issues/78#issuecomment-414132091  

Some information I found:  
http://releases.llvm.org/3.6.2/tools/docs/UsersManual.html  
> clang supports the Microsoft #pragma comment(lib, "foo.lib") feature for automatically linking against the specified library. Currently this feature only works with the Visual C++ linker.  
  
I would also like to see an additional CI build job added that forces b2 to use clang-cl and link dynamically in order to prove this fix.  This is something I could roll into all of the CMT repos eventually, since the appveyor.yml is repository-specific there is no easy way to update them all, but we could start here.

---

## Comment 3

> Username: jeking3  
> Created at: 2019-05-14 11:38:54 UTC  
> Url: https://github.com/boostorg/uuid/issues/78#issuecomment-492201586  

I was able to reproduce this in a CI build job.  
  
https://ci.appveyor.com/project/jeking3/uuid/builds/24529878/job/sgb58e1rq28pbhmu
