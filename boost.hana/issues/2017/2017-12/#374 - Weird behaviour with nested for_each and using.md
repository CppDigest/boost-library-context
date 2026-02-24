# #374 - Weird behaviour with nested for_each and using [Closed]

> Username: hovren  
> Created at: 2017-12-08 13:35:01 UTC  
> Updated at: 2017-12-11 18:00:25 UTC  
> Closed at: 2017-12-11 18:00:25 UTC  
> Url: https://github.com/boostorg/hana/issues/374  

I am experiencing something really funky when I am using a for_each inside another for_each together with unpacking of the types, when the outer type is templated:  
  
```  
template<typename T>  
struct Foo {  
  T x;  
  static constexpr const char* CLASS_ID = "Foo";  
};  
  
  
int main() {  
  auto temptypes = tuple_t<template_t<Foo>>;  
  auto dtypes = tuple_t<char, int, double>;  
  
  for_each(temptypes, [&](auto t){  
    using T = typename decltype(t)::type;  
    auto tt = T();  
    auto ttd = tt(type_c<double>); // Foo<double>, ...  
    using TDouble = typename decltype(ttd)::type;  
    std::cout << "Outer: " << TDouble::CLASS_ID << std::endl;  
  
    for_each(dtypes, [&](auto d){  
      using D = typename decltype(d)::type;  
  
      // FAILS:  
      std::cout << "Nested: " << TDouble::CLASS_ID << std::endl;  
    });  
  
  });  
}  
```  
This fails with the following error  
```  
error: ‘CLASS_ID’ is not a member of ‘boost::hana::basic_type<char>::type {aka char}’  
       std::cout << "Nested: " << TDouble::CLASS_ID << std::endl;  
```  
I guess this means TDouble is now referring to the type D, which is a bit surprising...  
  
This problem only happens if the outer types are templated.  
  
I can work around the issue by again specifying the TDouble type within the nested loop:  
  
```  
for_each(dtypes, [&](auto d){  
     using D = typename decltype(d)::type;  
      
    // Redefine the type  
    auto traj_double = traj_template(hana::type_c<double>);  
    using TrajectoryImpl = typename decltype(traj_double)::type;  
        
     // OK!  
     std::cout << "Nested: " << TDouble::CLASS_ID << std::endl;  
});  
```  
  
Sidenote: The way I am creating the type specialization for the Foo type seems a bit contrived. Is there a simpler way?

---

## Comment 1

> Username: ricejasonf  
> Created at: 2017-12-08 18:48:18 UTC  
> Url: https://github.com/boostorg/hana/issues/374#issuecomment-350341383  

The first code block compiles for me on clang++ 3.9. What compiler are you using?  
  
To address your sidenote, if you are just trying to apply each type to a list of templates, you can use [`hana::ap`](http://boostorg.github.io/hana/group__group-Applicative.html#ga6176a32953dbbdee96e3ab3c73e4c890).  
  
```cpp  
#include <boost/hana.hpp>  
  
namespace hana = boost::hana;  
  
template<typename T>  
struct Foo {  
  T x;  
  static constexpr const char* CLASS_ID = "Foo";  
};  
  
template<typename T>  
struct Bar {  
  T x;  
  static constexpr const char* CLASS_ID = "Bar";  
};  
  
  
int main() {  
  auto temptypes = hana::tuple<hana::template_t<Foo>, hana::template_t<Bar>>{};  
  auto dtypes = hana::tuple_t<char, int, double>;  
  
  BOOST_HANA_CONSTANT_ASSERT(hana::equal(  
    hana::ap(temptypes, dtypes)  
  , hana::tuple_t<  
      Foo<char>, Foo<int>, Foo<double>  
    , Bar<char>, Bar<int>, Bar<double>  
    >  
  ));  
}  
```  
Note that `temptypes` is not using `tuple_t` which creates a `hana::tuple` with every element wrapped in `hana::type`.

---

## Comment 2

> Username: hovren  
> Created at: 2017-12-09 10:02:17 UTC  
> Url: https://github.com/boostorg/hana/issues/374#issuecomment-350442020  

I am using GCC 6.4.1.

---

## Comment 3

> Username: ricejasonf  
> Created at: 2017-12-09 18:00:30 UTC  
> Url: https://github.com/boostorg/hana/issues/374#issuecomment-350494374  

I can't reproduce the error you were getting using GCC 6.1.1. It compiles and prints just fine.  
```  
Outer: Foo  
Nested: Foo  
Nested: Foo  
Nested: Foo  
```

---

## Comment 4

> Username: hovren  
> Created at: 2017-12-09 19:18:27 UTC  
> Url: https://github.com/boostorg/hana/issues/374#issuecomment-350499213  

I can confirm that compiling with `clang++ -std=c++14 nested_for_each.cc -o test` compiles and works as expected. Switching from `clang++` to `g++` still fails.  
`g++ --version` reports `g++ (GCC) 6.4.1 20170727 (Red Hat 6.4.1-1)`  
  
The full error is as follows:  
  
```  
In instantiation of ‘main()::<lambda(auto:1)>::<lambda(auto:2)> [with auto:2 = boost::hana::type_impl<char>::_; auto:1 = boost::hana::type_impl<boost::hana::template_t<Foo> >::_]’:  
./hana/include/boost/hana/for_each.hpp:46:45:   required from ‘constexpr void boost::hana::detail::on_each<F>::operator()(Xs&& ...) const [with Xs = {boost::hana::type_impl<char>::_&, boost::hana::type_impl<int>::_&, boost::hana::type_impl<double>::_&}; F = main()::<lambda(auto:1)> [with auto:1 = boost::hana::type_impl<boost::hana::template_t<Foo> >::_]::<lambda(auto:2)>*]’  
./hana/include/boost/hana/basic_tuple.hpp:117:39:   required from ‘static constexpr decltype(auto) boost::hana::unpack_impl<boost::hana::basic_tuple_tag>::apply(boost::hana::detail::basic_tuple_impl<std::integer_sequence<long unsigned int, _Idx ...>, Xn ...>&, F&&) [with long unsigned int ...i = {0ul, 1ul, 2ul}; Xn = {boost::hana::type_impl<char>::_, boost::hana::type_impl<int>::_, boost::hana::type_impl<double>::_}; F = boost::hana::detail::on_each<main()::<lambda(auto:1)> [with auto:1 = boost::hana::type_impl<boost::hana::template_t<Foo> >::_]::<lambda(auto:2)>*>]’  
./hana/include/boost/hana/unpack.hpp:47:29:   required from ‘constexpr decltype(auto) boost::hana::unpack_t::operator()(Xs&&, F&&) const [with Xs = boost::hana::basic_tuple<boost::hana::type_impl<char>::_, boost::hana::type_impl<int>::_, boost::hana::type_impl<double>::_>&; F = boost::hana::detail::on_each<main()::<lambda(auto:1)> [with auto:1 = boost::hana::type_impl<boost::hana::template_t<Foo> >::_]::<lambda(auto:2)>*>]’  
./hana/include/boost/hana/tuple.hpp:225:32:   required from ‘static constexpr decltype(auto) boost::hana::unpack_impl<boost::hana::tuple_tag>::apply(Xs&&, F&&) [with Xs = boost::hana::tuple<boost::hana::type_impl<char>::_, boost::hana::type_impl<int>::_, boost::hana::type_impl<double>::_>&; F = boost::hana::detail::on_each<main()::<lambda(auto:1)> [with auto:1 = boost::hana::type_impl<boost::hana::template_t<Foo> >::_]::<lambda(auto:2)>*>]’  
./hana/include/boost/hana/unpack.hpp:47:29:   required from ‘constexpr decltype(auto) boost::hana::unpack_t::operator()(Xs&&, F&&) const [with Xs = boost::hana::tuple<boost::hana::type_impl<char>::_, boost::hana::type_impl<int>::_, boost::hana::type_impl<double>::_>&; F = boost::hana::detail::on_each<main()::<lambda(auto:1)> [with auto:1 = boost::hana::type_impl<boost::hana::template_t<Foo> >::_]::<lambda(auto:2)>*>]’  
./hana/include/boost/hana/for_each.hpp:56:25:   [ skipping 5 instantiation contexts, use -ftemplate-backtrace-limit=0 to disable ]  
./hana/include/boost/hana/unpack.hpp:47:29:   required from ‘constexpr decltype(auto) boost::hana::unpack_t::operator()(Xs&&, F&&) const [with Xs = boost::hana::basic_tuple<boost::hana::type_impl<boost::hana::template_t<Foo> >::_>&; F = boost::hana::detail::on_each<main()::<lambda(auto:1)>*>]’  
./hana/include/boost/hana/tuple.hpp:225:32:   required from ‘static constexpr decltype(auto) boost::hana::unpack_impl<boost::hana::tuple_tag>::apply(Xs&&, F&&) [with Xs = boost::hana::tuple<boost::hana::type_impl<boost::hana::template_t<Foo> >::_>&; F = boost::hana::detail::on_each<main()::<lambda(auto:1)>*>]’  
./hana/include/boost/hana/unpack.hpp:47:29:   required from ‘constexpr decltype(auto) boost::hana::unpack_t::operator()(Xs&&, F&&) const [with Xs = boost::hana::tuple<boost::hana::type_impl<boost::hana::template_t<Foo> >::_>&; F = boost::hana::detail::on_each<main()::<lambda(auto:1)>*>]’  
./hana/include/boost/hana/for_each.hpp:56:25:   required from ‘static constexpr void boost::hana::for_each_impl<T, boost::hana::when<condition> >::apply(Xs&&, F&&) [with Xs = boost::hana::tuple<boost::hana::type_impl<boost::hana::template_t<Foo> >::_>&; F = main()::<lambda(auto:1)>; T = boost::hana::tuple_tag; bool condition = true]’  
./hana/include/boost/hana/for_each.hpp:35:30:   required from ‘constexpr void boost::hana::for_each_t::operator()(Xs&&, F&&) const [with Xs = boost::hana::tuple<boost::hana::type_impl<boost::hana::template_t<Foo> >::_>&; F = main()::<lambda(auto:1)>]’  
nested_for_each.cc:31:4:   required from here  
nested_for_each.cc:28:31: error: ‘CLASS_ID’ is not a member of ‘boost::hana::basic_type<char>::type {aka char}’  
       std::cout << "Nested: " << TDouble::CLASS_ID << std::endl;  
       ~~~~~~~~~~~~~~~~~~~~~~~~^~~~~~~~~~  
In file included from ./hana/include/boost/hana.hpp:110:0,  
                 from nested_for_each.cc:2:  
./hana/include/boost/hana/for_each.hpp: In instantiation of ‘constexpr void boost::hana::detail::on_each<F>::operator()(Xs&& ...) const [with Xs = {boost::hana::type_impl<char>::_&, boost::hana::type_impl<int>::_&, boost::hana::type_impl<double>::_&}; F = main()::<lambda(auto:1)> [with auto:1 = boost::hana::type_impl<boost::hana::template_t<Foo> >::_]::<lambda(auto:2)>*]’:  
./hana/include/boost/hana/basic_tuple.hpp:117:39:   required from ‘static constexpr decltype(auto) boost::hana::unpack_impl<boost::hana::basic_tuple_tag>::apply(boost::hana::detail::basic_tuple_impl<std::integer_sequence<long unsigned int, _Idx ...>, Xn ...>&, F&&) [with long unsigned int ...i = {0ul, 1ul, 2ul}; Xn = {boost::hana::type_impl<char>::_, boost::hana::type_impl<int>::_, boost::hana::type_impl<double>::_}; F = boost::hana::detail::on_each<main()::<lambda(auto:1)> [with auto:1 = boost::hana::type_impl<boost::hana::template_t<Foo> >::_]::<lambda(auto:2)>*>]’  
./hana/include/boost/hana/unpack.hpp:47:29:   required from ‘constexpr decltype(auto) boost::hana::unpack_t::operator()(Xs&&, F&&) const [with Xs = boost::hana::basic_tuple<boost::hana::type_impl<char>::_, boost::hana::type_impl<int>::_, boost::hana::type_impl<double>::_>&; F = boost::hana::detail::on_each<main()::<lambda(auto:1)> [with auto:1 = boost::hana::type_impl<boost::hana::template_t<Foo> >::_]::<lambda(auto:2)>*>]’  
./hana/include/boost/hana/tuple.hpp:225:32:   required from ‘static constexpr decltype(auto) boost::hana::unpack_impl<boost::hana::tuple_tag>::apply(Xs&&, F&&) [with Xs = boost::hana::tuple<boost::hana::type_impl<char>::_, boost::hana::type_impl<int>::_, boost::hana::type_impl<double>::_>&; F = boost::hana::detail::on_each<main()::<lambda(auto:1)> [with auto:1 = boost::hana::type_impl<boost::hana::template_t<Foo> >::_]::<lambda(auto:2)>*>]’  
./hana/include/boost/hana/unpack.hpp:47:29:   required from ‘constexpr decltype(auto) boost::hana::unpack_t::operator()(Xs&&, F&&) const [with Xs = boost::hana::tuple<boost::hana::type_impl<char>::_, boost::hana::type_impl<int>::_, boost::hana::type_impl<double>::_>&; F = boost::hana::detail::on_each<main()::<lambda(auto:1)> [with auto:1 = boost::hana::type_impl<boost::hana::template_t<Foo> >::_]::<lambda(auto:2)>*>]’  
./hana/include/boost/hana/for_each.hpp:56:25:   required from ‘static constexpr void boost::hana::for_each_impl<T, boost::hana::when<condition> >::apply(Xs&&, F&&) [with Xs = boost::hana::tuple<boost::hana::type_impl<char>::_, boost::hana::type_impl<int>::_, boost::hana::type_impl<double>::_>&; F = main()::<lambda(auto:1)> [with auto:1 = boost::hana::type_impl<boost::hana::template_t<Foo> >::_]::<lambda(auto:2)>; T = boost::hana::tuple_tag; bool condition = true]’  
./hana/include/boost/hana/for_each.hpp:35:30:   [ skipping 4 instantiation contexts, use -ftemplate-backtrace-limit=0 to disable ]  
./hana/include/boost/hana/unpack.hpp:47:29:   required from ‘constexpr decltype(auto) boost::hana::unpack_t::operator()(Xs&&, F&&) const [with Xs = boost::hana::basic_tuple<boost::hana::type_impl<boost::hana::template_t<Foo> >::_>&; F = boost::hana::detail::on_each<main()::<lambda(auto:1)>*>]’  
./hana/include/boost/hana/tuple.hpp:225:32:   required from ‘static constexpr decltype(auto) boost::hana::unpack_impl<boost::hana::tuple_tag>::apply(Xs&&, F&&) [with Xs = boost::hana::tuple<boost::hana::type_impl<boost::hana::template_t<Foo> >::_>&; F = boost::hana::detail::on_each<main()::<lambda(auto:1)>*>]’  
./hana/include/boost/hana/unpack.hpp:47:29:   required from ‘constexpr decltype(auto) boost::hana::unpack_t::operator()(Xs&&, F&&) const [with Xs = boost::hana::tuple<boost::hana::type_impl<boost::hana::template_t<Foo> >::_>&; F = boost::hana::detail::on_each<main()::<lambda(auto:1)>*>]’  
./hana/include/boost/hana/for_each.hpp:56:25:   required from ‘static constexpr void boost::hana::for_each_impl<T, boost::hana::when<condition> >::apply(Xs&&, F&&) [with Xs = boost::hana::tuple<boost::hana::type_impl<boost::hana::template_t<Foo> >::_>&; F = main()::<lambda(auto:1)>; T = boost::hana::tuple_tag; bool condition = true]’  
./hana/include/boost/hana/for_each.hpp:35:30:   required from ‘constexpr void boost::hana::for_each_t::operator()(Xs&&, F&&) const [with Xs = boost::hana::tuple<boost::hana::type_impl<boost::hana::template_t<Foo> >::_>&; F = main()::<lambda(auto:1)>]’  
nested_for_each.cc:31:4:   required from here  
./hana/include/boost/hana/for_each.hpp:46:45: error: use of ‘main()::<lambda(auto:1)>::<lambda(auto:2)> [with auto:2 = boost::hana::type_impl<int>::_; auto:1 = boost::hana::type_impl<boost::hana::template_t<Foo> >::_]’ before deduction of ‘auto’  
                 (void)Swallow{0, ((void)(*f)(static_cast<Xs&&>(xs)), 0)...};  
  
```

---

## Comment 5

> Username: ricejasonf  
> Created at: 2017-12-09 19:41:33 UTC  
> Url: https://github.com/boostorg/hana/issues/374#issuecomment-350500611  

Yeah on wandbox both gcc 6.3 and 7.1 do this, however gcc 8 does not.  
  
https://wandbox.org/permlink/e2tTSP7lUOyn1MoA  
  
Super weird compiler bug

---

## Comment 6

> Username: ldionne  
> Created at: 2017-12-11 18:00:25 UTC  
> Url: https://github.com/boostorg/hana/issues/374#issuecomment-350805652  

Closing as invalid because this is not a bug in Hana per se, but in the compiler. FWIW, I think I've seen this behavior before.
