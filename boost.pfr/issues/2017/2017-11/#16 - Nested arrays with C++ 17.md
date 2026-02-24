# #16 - Nested arrays with C++ 17. [Closed]

> Username: kyku  
> Created at: 2017-11-16 17:09:37 UTC  
> Updated at: 2017-12-24 17:28:31 UTC  
> Closed at: 2017-11-27 10:02:51 UTC  
> Url: https://github.com/boostorg/pfr/issues/16  

The following snippet compiles fine in C++14 mode but fails in C++17:  
  
```  
#include <boost/pfr/precise/core.hpp>  
  
int main()  
{  
  struct foo  
  {  
    int things[3];  
  };  
    
  boost::pfr::for_each_field(foo{{1, 2, 3}}, [&](auto& field) {});  
}  
```  
  
Tested on GCC 7.2.0 and CLANG 5.0.0.  
  
Errors from CLANG:  
  
> In file included from main.cpp:5:  
> In file included from include/boost/pfr/precise/core.hpp:21:  
> In file included from include/boost/pfr/detail/core17.hpp:10:  
> include/boost/pfr/detail/core17_generated.hpp:57:9: error: type 'foo' decomposes into 1 elements, but 3 names were provided  
>   auto& [a,b,c] = val;  
>         ^  
> include/boost/pfr/detail/core17_generated.hpp:1032:30: note: in instantiation of function template specialization 'boost::pfr::detail::tie_as_tuple<foo>'  
>       requested here  
>   return boost::pfr::detail::tie_as_tuple(val, fields_count_tag{});  
>                              ^  
> include/boost/pfr/detail/core17.hpp:52:17: note: in instantiation of function template specialization 'boost::pfr::detail::tie_as_tuple<foo>' requested here  
>         detail::tie_as_tuple(t)  
>                 ^  
> include/boost/pfr/precise/core.hpp:145:27: note: in instantiation of function template specialization 'boost::pfr::detail::for_each_field_dispatcher<foo,  
>       (lambda at include/boost/pfr/precise/core.hpp:147:9), 0, 1, 2>' requested here  
>     ::boost::pfr::detail::for_each_field_dispatcher(  
>                           ^  
> main.cpp:102:15: note: in instantiation of function template specialization 'boost::pfr::for_each_field<foo, (lambda at main.cpp:102:46)>' requested here  
>   boost::pfr::for_each_field(foo{{1, 2, 3}}, [&](auto& field) {});  
>               ^  
> 1 error generated.  
  
Errors from GCC:  
  
> In file included from include/boost/pfr/detail/core17.hpp:10:0,  
                 from include/boost/pfr/precise/core.hpp:21,  
                 from main.cpp:5:  
include/boost/pfr/detail/core17_generated.hpp: In instantiation of ‘constexpr auto boost::pfr::detail::tie_as_tuple(T&, boost::pfr::detail::size_t_<3>) [with T = main()::foo; boost::pfr::detail::size_t_<3> = std::integral_constant<long unsigned int, 3>]’:  
include/boost/pfr/detail/core17_generated.hpp:1032:42:   required from ‘constexpr auto boost::pfr::detail::tie_as_tuple(T&) [with T = main()::foo]’  
include/boost/pfr/detail/core17.hpp:52:29:   required from ‘void boost::pfr::detail::for_each_field_dispatcher(T&, F&&, std::index_sequence<_Idx ...>) [with T = main()::foo; F = boost::pfr::for_each_field(T&&, F&&) [with T = main()::foo; F = main()::<lambda(auto:6&)>]::<lambda(auto:1&&)>; long unsigned int ...I = {0, 1, 2}; std::index_sequence<_Idx ...> = std::integer_sequence<long unsigned int, 0, 1, 2>]’  
include/boost/pfr/precise/core.hpp:145:52:   required from ‘void boost::pfr::for_each_field(T&&, F&&) [with T = main()::foo; F = main()::<lambda(auto:6&)>]’  
main.cpp:102:65:   required from here  
include/boost/pfr/detail/core17_generated.hpp:57:9: error: 3 names provided while ‘main()::foo’ decomposes into 1 elements  
   auto& [a,b,c] = val;  
         ^~~~~~~  
In file included from include/boost/pfr/precise/core.hpp:21:0,  
                 from main.cpp:5:  
include/boost/pfr/detail/core17.hpp: In instantiation of ‘void boost::pfr::detail::for_each_field_dispatcher(T&, F&&, std::index_sequence<_Idx ...>) [with T = main()::foo; F = boost::pfr::for_each_field(T&&, F&&) [with T = main()::foo; F = main()::<lambda(auto:6&)>]::<lambda(auto:1&&)>; long unsigned int ...I = {0, 1, 2}; std::index_sequence<_Idx ...> = std::integer_sequence<long unsigned int, 0, 1, 2>]’:  
include/boost/pfr/precise/core.hpp:145:52:   required from ‘void boost::pfr::for_each_field(T&&, F&&) [with T = main()::foo; F = main()::<lambda(auto:6&)>]’  
main.cpp:102:65:   required from here  
include/boost/pfr/detail/core17.hpp:51:23: error: invalid use of void expression  
     std::forward<F>(f)(  
     ~~~~~~~~~~~~~~~~~~^  
         detail::tie_as_tuple(t)  
         ~~~~~~~~~~~~~~~~~~~~~~~  
     );  
     ~

---

## Comment 1

> Username: apolukhin  
> Created at: 2017-11-24 20:21:45 UTC  
> Url: https://github.com/boostorg/pfr/issues/16#issuecomment-346891984  

That's a known issue and it is documented (for example [here](http://apolukhin.github.io/magic_get/boost_precise_and_flat_reflectio/tutorial.html#boost_precise_and_flat_reflectio.tutorial.flat_or_precise_functions_to_cho))  
  
If you know some way to workaround that issue or to static_assert it, then please give me hint.

---

## Comment 2

> Username: kasper93  
> Created at: 2017-12-24 17:10:42 UTC  
> Updated at: 2017-12-24 17:28:31 UTC  
> Url: https://github.com/boostorg/pfr/issues/16#issuecomment-353794767  

@apolukhin: You could test explicit initialization of every member instead one aggregate list.   
  
Let's consider this example:  
``` cpp  
struct Foo  
{  
    int a;  
    int b[3];  
    int c;  
};  
```  
You can construct it like so  
``` cpp  
Foo foo{1, 1, 1, 1, 1};  
```  
which will of course give you "flat" number, brace elision is coming into play. But if you instead do this:  
``` cpp  
Foo foo{{}, {}, {}};  
```  
you will get "precise" number of members. Because as you probably figured this will fail:  
``` cpp  
Foo foo{{}, {}, {}, {}};  
```  
  
I can make PR in new year (should have time then to test it properly ;p) with the changes to support precise mode with nested arrays. Unless you beat me to it :>
