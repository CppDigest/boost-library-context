# #145 - [lwt] `BOOST_TEST_TRAIT` fails to compile if the trait involves an incomplete type [Closed]

> Username: Lastique  
> Created at: 2023-05-20 20:48:45 UTC  
> Updated at: 2023-05-21 12:10:06 UTC  
> Closed at: 2023-05-21 12:10:06 UTC  
> Url: https://github.com/boostorg/core/issues/145  

[Here's](https://gcc.godbolt.org/z/Ya5v1Mqed) an example:  
  
```  
#include <boost/core/lightweight_test_trait.hpp>  
  
template< typename T >  
struct trait  
{  
    static constexpr bool value = false;  
};  
  
struct incomplete;  
  
int main()  
{  
    BOOST_TEST_TRAIT_FALSE((trait< incomplete >));  
    return boost::report_errors();  
}  
```  
  
```  
In file included from /opt/compiler-explorer/libs/boost_1_82_0/boost/core/lightweight_test_trait.hpp:24,  
                 from <source>:1:  
/opt/compiler-explorer/libs/boost_1_82_0/boost/core/type_name.hpp: In instantiation of 'std::string boost::core::detail::typeid_name() [with T = incomplete; std::string = std::__cxx11::basic_string<char>]':  
/opt/compiler-explorer/libs/boost_1_82_0/boost/core/type_name.hpp:214:30:   required from 'static std::string boost::core::detail::tn_holder<T>::type_name(const std::string&) [with T = incomplete; std::string = std::__cxx11::basic_string<char>]'  
/opt/compiler-explorer/libs/boost_1_82_0/boost/core/type_name.hpp:499:34:   required from 'int boost::core::detail::tn_add_each_impl(std::string&) [with T = incomplete; std::string = std::__cxx11::basic_string<char>]'  
/opt/compiler-explorer/libs/boost_1_82_0/boost/core/type_name.hpp:508:36:   required from 'std::string boost::core::detail::tn_add_each() [with T = {incomplete}; std::string = std::__cxx11::basic_string<char>]'  
/opt/compiler-explorer/libs/boost_1_82_0/boost/core/type_name.hpp:1057:43:   required from 'static std::string boost::core::detail::tn_holder<L<T ...> >::type_name(const std::string&) [with L = trait; T = {incomplete}; std::string = std::__cxx11::basic_string<char>]'  
/opt/compiler-explorer/libs/boost_1_82_0/boost/core/type_name.hpp:1159:49:   required from 'std::string boost::core::type_name() [with T = trait<incomplete>; std::string = std::__cxx11::basic_string<char>]'  
/opt/compiler-explorer/libs/boost_1_82_0/boost/core/lightweight_test_trait.hpp:44:41:   required from 'void boost::detail::test_trait_impl(const char*, void (*)(T), bool, const char*, int, const char*) [with T = trait<incomplete>]'  
<source>:13:5:   required from here  
/opt/compiler-explorer/libs/boost_1_82_0/boost/core/type_name.hpp:107:29: error: invalid use of incomplete type 'struct incomplete'  
  107 |     return fix_typeid_name( typeid(T).name() );  
      |                             ^~~~~~~~~  
<source>:9:8: note: forward declaration of 'struct incomplete'  
    9 | struct incomplete;  
      |        ^~~~~~~~~~  
```  
  
The test intentionally uses an incomplete type to test the trait's behavior wrt. incomplete types.

---

## Comment 1

> Username: Lastique  
> Created at: 2023-05-21 01:14:31 UTC  
> Url: https://github.com/boostorg/core/issues/145#issuecomment-1556056843  

Actually, there are problems with `typeid` besides the incomplete types, see [here](https://github.com/boostorg/iterator/actions/runs/5034214590/jobs/9028899849#step:7:2802), for example. Looks like `boost::core::type_name()` is broken for member function types as well.

---

## Comment 2

> Username: Lastique  
> Created at: 2023-05-21 01:20:47 UTC  
> Url: https://github.com/boostorg/core/issues/145#issuecomment-1556057762  

Why do we need `boost::core::type_name` again? I thought `demangle` would be enough.

---

## Comment 3

> Username: pdimov  
> Created at: 2023-05-21 01:27:26 UTC  
> Url: https://github.com/boostorg/core/issues/145#issuecomment-1556058586  

Looks like `typeid` doesn't work on qualified member functions, but only under C++03 and for Clang after 10.

---

## Comment 4

> Username: pdimov  
> Created at: 2023-05-21 01:33:40 UTC  
> Url: https://github.com/boostorg/core/issues/145#issuecomment-1556059459  

See https://godbolt.org/z/M5s348jxf for example. Under MSVC, it also cleans up things like `struct X`, `__cdecl`, `__ptr64` and other cruft.

---

## Comment 5

> Username: Lastique  
> Created at: 2023-05-21 10:24:56 UTC  
> Url: https://github.com/boostorg/core/issues/145#issuecomment-1556141877  

> See https://godbolt.org/z/M5s348jxf for example. Under MSVC, it also cleans up things like `struct X`, `__cdecl`, `__ptr64` and other cruft.  
  
This could be achieved by string modification instead of decomposing types and generating `type_info` structures for each component.

---

## Comment 6

> Username: pdimov  
> Created at: 2023-05-21 11:57:52 UTC  
> Url: https://github.com/boostorg/core/issues/145#issuecomment-1556160787  

Both cases should be fixed on develop.  
  
> This could be achieved by string modification instead of decomposing types and generating type_info structures for each component.  
  
Maybe... but I don't think it would be particularly easy. There are a lot of other weird differences between the demangled `typeid(T).name()` such as whitespace placement, `[]` being `[0]`, `unsigned long long` being `unsigned __int64`, etc, and figuring out `std::map<std::string, std::string>` would basically require parsing the string back into a type structure, or doing creative things with regex capture matching. `type_name` takes care of all that. It also works with -fno-rtti and can do slightly better than just giving up.  
  
In addition, there are odd cases where `typeid(T).name()` doesn't work, such as incomplete types (fixed now thanks to you) and e.g. `std::pair<void, void>`.  
  
All this is admittedly overkill for the purposes of BOOST_TEST_TRAIT, looking at its output was just what motivated me to write `type_name`.

---

## Comment 7

> Username: Lastique  
> Created at: 2023-05-21 12:10:06 UTC  
> Url: https://github.com/boostorg/core/issues/145#issuecomment-1556163934  

> Both cases should be fixed on develop.  
  
Thank you. Although I have already switched to `BOOST_TEST` in these test cases.
