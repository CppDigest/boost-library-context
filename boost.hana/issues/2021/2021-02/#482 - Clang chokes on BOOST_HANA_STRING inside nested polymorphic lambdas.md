# #482 - Clang chokes on BOOST_HANA_STRING inside nested polymorphic lambdas [Closed]

> Username: BillyDonahue  
> Created at: 2021-02-15 08:02:07 UTC  
> Updated at: 2022-10-21 02:54:45 UTC  
> Closed at: 2022-10-21 02:54:45 UTC  
> Url: https://github.com/boostorg/hana/issues/482  

Clang chokes when `BOOST_HANA_STRING` is enclosed by 2 layers of polymorphic lambdas.  
  
`cannot initialize return object of type 'decltype(auto)' with an lvalue of type 'const char [4]'`  
  
Tested with Boost-1.75.0, Clang-11.0.1, MacOS & CompilerExplorer. GCC not affected.  
https://gcc.godbolt.org/z/6WsY7e  
  
If either of the lambdas is not polymorphic, there's no problem.  
  
  
```c++  
#include <cstdio>  
#include <boost/hana.hpp>  
  
template <typename T>  
void print(T&& t) {  
    printf(t.c_str());  
}  
  
void ok() {  
    [](auto) {  
        [](int) {  
            auto str = BOOST_HANA_STRING("xxx");  
            printf("%s\n", str.c_str());  
        }(2);  
    }(1);  
}  
  
void err() {  
    [](auto) {  
        [](auto) {  // <== changed to auto and now build fails under clang.  
            auto str = BOOST_HANA_STRING("xxx");  
            printf("%s\n", str.c_str());  
        }(2);  
    }(1);  
}  
  
int main() {  
    ok();  
    err();  
    return 0;  
}  
```  
https://raw.githubusercontent.com/BillyDonahue/experimental/hana_repro/per_file_template/hana_string_repro.cpp  
  
  
```  
<source>:21:42: error: cannot initialize return object of type 'decltype(auto)' with an lvalue of type 'const char [4]'  
            auto str = BOOST_HANA_STRING("xxx");  
                                         ^~~~~  
/opt/compiler-explorer/libs/boost_1_75_0/boost/hana/string.hpp:105:60: note: expanded from macro 'BOOST_HANA_STRING'  
            static constexpr decltype(auto) get() { return s; }             \  
                                                           ^  
<source>:21:24: note: in instantiation of member function 'err()::(anonymous class)::operator()(int)::(anonymous class)::operator()(auto)::(anonymous class)::operator()()::tmp::get' requested here  
            auto str = BOOST_HANA_STRING("xxx");  
                       ^  
/opt/compiler-explorer/libs/boost_1_75_0/boost/hana/string.hpp:104:16: note: expanded from macro 'BOOST_HANA_STRING'  
        struct tmp {                                                        \  
               ^  
<source>:24:6: note: in instantiation of function template specialization 'err()::(anonymous class)::operator()<int>' requested here  
    }(1);  
     ^  
1 error generated.  
```

---

## Comment 1

> Username: ldionne  
> Created at: 2022-10-21 02:54:45 UTC  
> Url: https://github.com/boostorg/hana/issues/482#issuecomment-1286391671  

This definitely looks like a compiler bug to me, can you please report against Clang?  
  
Here's a reduced reproducer: https://godbolt.org/z/d4hMevrPo  
  
```  
int main() {  
    // OK  
    [](auto) {  
        [](int) {  
            struct tmp {                                                          
                static constexpr auto get() { return "xxx"; }           
            };  
        }(2);  
    }(1);  
  
    // NOT OK  
    [](auto) {  
        [](auto) {  
            struct tmp {                                                          
                static constexpr auto get() { return "xxx"; }           
            };  
        }(2);  
    }(1);  
}  
```
