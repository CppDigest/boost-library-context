# #485 - BOOST_JSON_STANDALONE results in undefined references to destructors [Closed]

> Username: justinjereza  
> Created at: 2021-01-20 16:30:36 UTC  
> Updated at: 2021-01-20 16:44:11 UTC  
> Closed at: 2021-01-20 16:44:10 UTC  
> Url: https://github.com/boostorg/json/issues/485  

Fedora 33 / GCC 10.2.1 C++20 / Conan Boost 1.75 2d040665a7cb2820e00fe685a77c8169fcc3b764  
  
When compiling with `BOOST_JSON_STANDALONE`, linker errors appear with an undefined reference to ~value(). As I understand it, there is no need to link `libboost_json.a` when standalone mode is enabled. I tried to link it as well anyway and the result is the same.  
  
I have been able to trim it down to the following test case:  
```  
#include <boost/json.hpp>  
  
int main() {  
    boost::json::value v{};  
    return 0;  
}  
```  
  
It results in the following linker error:  
```  
/usr/bin/ld: CMakeFiles/json-standalone.dir/JsonStandalone.cxx.o: in function `main':  
/root/test-cases/JsonStandalone.cxx:4: undefined reference to `boost::json::standalone::value::~value()'  
collect2: error: ld returned 1 exit status  
```  
  
I have also tried compiling it directly and it results in the same error:  
```  
bash-5.0# g++ -std=c++20 -I/root/.conan/data/boost/1.75.0/_/_/package/2d040665a7cb2820e00fe685a77c8169fcc3b764/include -DBOOST_JSON_STANDALONE JsonStandalone.cxx   
/usr/bin/ld: /tmp/ccDoEwjC.o: in function `main':  
JsonStandalone.cxx:(.text+0x22): undefined reference to `boost::json::standalone::value::~value()'  
collect2: error: ld returned 1 exit status  
```

---

## Comment 1

> Username: vinniefalco  
> Created at: 2021-01-20 16:35:02 UTC  
> Url: https://github.com/boostorg/json/issues/485#issuecomment-763768554  

Why do you think there is no need to link to a library? You still need to link to a version of the library built using the stand-alone mode. Note that standalone is not a Boost feature so you would have to build the library yourself. Alternatively you could use the header-only mode (which can also work with standalone). See:  
https://www.boost.org/doc/libs/develop/libs/json/doc/html/json/overview.html#json.overview.requirements.header_only

---

## Comment 2

> Username: justinjereza  
> Created at: 2021-01-20 16:44:10 UTC  
> Url: https://github.com/boostorg/json/issues/485#issuecomment-763775594  

Ah... my bad. I missed that part of the documentation. It looks like the library I was linking is not built for standalone. Including `boost/json/src.hpp` resolved the linker problem.
