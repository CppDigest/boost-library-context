# #16 - test_construction failed on Dinkumware [Closed]

> Username: BinCaoWR  
> Created at: 2019-08-21 05:43:36 UTC  
> Updated at: 2022-09-07 18:49:54 UTC  
> Closed at: 2019-09-04 07:32:01 UTC  
> Url: https://github.com/boostorg/poly_collection/issues/16  

There are a number of tests from test_construction failed on Dinkumware. I see some tests in test_construction.cpp have worked around for BOOST_LIBSTDCXX_VERSION. Any adaptation needed for BOOST_DINKUMWARE_STDLIB?  
  
The Dinkumware version I'm using is 804:  
`BOOST_DINKUMWARE_STDLIB=804 [-W#pragma-messages]`  
`BOOST_STDLIB="Dinkumware standard library version " "804" [-W#pragma-messages]`  
  
The failed tests as below:  
`../libs/poly_collection/test/test_construction.cpp(97): test 'p3.get_allocator().comes_from(root2)' failed in function 'void test_allocator_aware_construction() [Propagate = true, AlwaysEqual = false, PolyCollection = boost::poly_collection::any_collection<boost::type_erasure::incrementable<boost::type_erasure::_self>, std::allocator<boost::type_erasure::any<boost::mpl::vector2<boost::type_erasure::incrementable<boost::type_erasure::_self>, boost::type_erasure::typeid_<boost::type_erasure::_self> >, boost::type_erasure::_self &> > >, ValueFactory = test_utilities::auto_increment, Types = <any_types::incrementable1, double, any_types::incrementable3, int, boost::type_erasure::any<boost::mpl::vector4<boost::type_erasure::copy_constructible<boost::type_erasure::_self>, boost::type_erasure::assignable<boost::type_erasure::_self, const boost::type_erasure::_self &>, boost::type_erasure::incrementable<boost::type_erasure::_self>, any_types::convertible_to_int<boost::type_erasure::_self> >, boost::type_erasure::_self>>]'  
../libs/poly_collection/test/test_construction.cpp(116): test 'p2.get_allocator().comes_from(Propagate?root1:root2)' failed in function 'void test_allocator_aware_construction() [Propagate = true, AlwaysEqual = false, PolyCollection = boost::poly_collection::any_collection<boost::type_erasure::incrementable<boost::type_erasure::_self>, std::allocator<boost::type_erasure::any<boost::mpl::vector2<boost::type_erasure::incrementable<boost::type_erasure::_self>, boost::type_erasure::typeid_<boost::type_erasure::_self> >, boost::type_erasure::_self &> > >, ValueFactory = test_utilities::auto_increment, Types = <any_types::incrementable1, double, any_types::incrementable3, int, boost::type_erasure::any<boost::mpl::vector4<boost::type_erasure::copy_constructible<boost::type_erasure::_self>, boost::type_erasure::assignable<boost::type_erasure::_self, const boost::type_erasure::_self &>, boost::type_erasure::incrementable<boost::type_erasure::_self>, any_types::convertible_to_int<boost::type_erasure::_self> >, boost::type_erasure::_self>>]'  
../libs/poly_collection/test/test_construction.cpp(116): test 'p2.get_allocator().comes_from(Propagate?root1:root2)' failed in function 'void test_allocator_aware_construction() [Propagate = true, AlwaysEqual = true, PolyCollection = boost::poly_collection::any_collection<boost::type_erasure::incrementable<boost::type_erasure::_self>, std::allocator<boost::type_erasure::any<boost::mpl::vector2<boost::type_erasure::incrementable<boost::type_erasure::_self>, boost::type_erasure::typeid_<boost::type_erasure::_self> >, boost::type_erasure::_self &> > >, ValueFactory = test_utilities::auto_increment, Types = <any_types::incrementable1, double, any_types::incrementable3, int, boost::type_erasure::any<boost::mpl::vector4<boost::type_erasure::copy_constructible<boost::type_erasure::_self>, boost::type_erasure::assignable<boost::type_erasure::_self, const boost::type_erasure::_self &>, boost::type_erasure::incrementable<boost::type_erasure::_self>, any_types::convertible_to_int<boost::type_erasure::_self> >, boost::type_erasure::_self>>]'  
../libs/poly_collection/test/test_construction.cpp(143): test 'p3.get_allocator().comes_from(Propagate?root1:root2)' failed in function 'void test_allocator_aware_construction() [Propagate = true, AlwaysEqual = true, PolyCollection = boost::poly_collection::any_collection<boost::type_erasure::incrementable<boost::type_erasure::_self>, std::allocator<boost::type_erasure::any<boost::mpl::vector2<boost::type_erasure::incrementable<boost::type_erasure::_self>, boost::type_erasure::typeid_<boost::type_erasure::_self> >, boost::type_erasure::_self &> > >, ValueFactory = test_utilities::auto_increment, Types = <any_types::incrementable1, double, any_types::incrementable3, int, boost::type_erasure::any<boost::mpl::vector4<boost::type_erasure::copy_constructible<boost::type_erasure::_self>, boost::type_erasure::assignable<boost::type_erasure::_self, const boost::type_erasure::_self &>, boost::type_erasure::incrementable<boost::type_erasure::_self>, any_types::convertible_to_int<boost::type_erasure::_self> >, boost::type_erasure::_self>>]'  
../libs/poly_collection/test/test_construction.cpp(179): test 'p2.get_allocator().comes_from(Propagate?root2:root1)' failed in function 'void test_allocator_aware_construction() [Propagate = true, AlwaysEqual = true, PolyCollection = boost::poly_collection::any_collection<boost::type_erasure::incrementable<boost::type_erasure::_self>, std::allocator<boost::type_erasure::any<boost::mpl::vector2<boost::type_erasure::incrementable<boost::type_erasure::_self>, boost::type_erasure::typeid_<boost::type_erasure::_self> >, boost::type_erasure::_self &> > >, ValueFactory = test_utilities::auto_increment, Types = <any_types::incrementable1, double, any_types::incrementable3, int, boost::type_erasure::any<boost::mpl::vector4<boost::type_erasure::copy_constructible<boost::type_erasure::_self>, boost::type_erasure::assignable<boost::type_erasure::_self, const boost::type_erasure::_self &>, boost::type_erasure::incrementable<boost::type_erasure::_self>, any_types::convertible_to_int<boost::type_erasure::_self> >, boost::type_erasure::_self>>]'  
../libs/poly_collection/test/test_construction.cpp(180): test 'p3.get_allocator().comes_from(Propagate?root1:root2)' failed in function 'void test_allocator_aware_construction() [Propagate = true, AlwaysEqual = true, PolyCollection = boost::poly_collection::any_collection<boost::type_erasure::incrementable<boost::type_erasure::_self>, std::allocator<boost::type_erasure::any<boost::mpl::vector2<boost::type_erasure::incrementable<boost::type_erasure::_self>, boost::type_erasure::typeid_<boost::type_erasure::_self> >, boost::type_erasure::_self &> > >, ValueFactory = test_utilities::auto_increment, Types = <any_types::incrementable1, double, any_types::incrementable3, int, boost::type_erasure::any<boost::mpl::vector4<boost::type_erasure::copy_constructible<boost::type_erasure::_self>, boost::type_erasure::assignable<boost::type_erasure::_self, const boost::type_erasure::_self &>, boost::type_erasure::incrementable<boost::type_erasure::_self>, any_types::convertible_to_int<boost::type_erasure::_self> >, boost::type_erasure::_self>>]'  
../libs/poly_collection/test/test_construction.cpp(97): test 'p3.get_allocator().comes_from(root2)' failed in function 'void test_allocator_aware_construction() [Propagate = true, AlwaysEqual = false, PolyCollection = boost::poly_collection::base_collection<base_types::base, std::allocator<base_types::base> >, ValueFactory = test_utilities::auto_increment, Types = <base_types::derived1, base_types::derived2, base_types::derived3, base_types::derived4, base_types::derived5>]'  
../libs/poly_collection/test/test_construction.cpp(116): test 'p2.get_allocator().comes_from(Propagate?root1:root2)' failed in function 'void test_allocator_aware_construction() [Propagate = true, AlwaysEqual = false, PolyCollection = boost::poly_collection::base_collection<base_types::base, std::allocator<base_types::base> >, ValueFactory = test_utilities::auto_increment, Types = <base_types::derived1, base_types::derived2, base_types::derived3, base_types::derived4, base_types::derived5>]'  
../libs/poly_collection/test/test_construction.cpp(116): test 'p2.get_allocator().comes_from(Propagate?root1:root2)' failed in function 'void test_allocator_aware_construction() [Propagate = true, AlwaysEqual = true, PolyCollection = boost::poly_collection::base_collection<base_types::base, std::allocator<base_types::base> >, ValueFactory = test_utilities::auto_increment, Types = <base_types::derived1, base_types::derived2, base_types::derived3, base_types::derived4, base_types::derived5>]'  
../libs/poly_collection/test/test_construction.cpp(143): test 'p3.get_allocator().comes_from(Propagate?root1:root2)' failed in function 'void test_allocator_aware_construction() [Propagate = true, AlwaysEqual = true, PolyCollection = boost::poly_collection::base_collection<base_types::base, std::allocator<base_types::base> >, ValueFactory = test_utilities::auto_increment, Types = <base_types::derived1, base_types::derived2, base_types::derived3, base_types::derived4, base_types::derived5>]'  
../libs/poly_collection/test/test_construction.cpp(179): test 'p2.get_allocator().comes_from(Propagate?root2:root1)' failed in function 'void test_allocator_aware_construction() [Propagate = true, AlwaysEqual = true, PolyCollection = boost::poly_collection::base_collection<base_types::base, std::allocator<base_types::base> >, ValueFactory = test_utilities::auto_increment, Types = <base_types::derived1, base_types::derived2, base_types::derived3, base_types::derived4, base_types::derived5>]'  
../libs/poly_collection/test/test_construction.cpp(180): test 'p3.get_allocator().comes_from(Propagate?root1:root2)' failed in function 'void test_allocator_aware_construction() [Propagate = true, AlwaysEqual = true, PolyCollection = boost::poly_collection::base_collection<base_types::base, std::allocator<base_types::base> >, ValueFactory = test_utilities::auto_increment, Types = <base_types::derived1, base_types::derived2, base_types::derived3, base_types::derived4, base_types::derived5>]'  
../libs/poly_collection/test/test_construction.cpp(97): test 'p3.get_allocator().comes_from(root2)' failed in function 'void test_allocator_aware_construction() [Propagate = true, AlwaysEqual = false, PolyCollection = boost::poly_collection::function_collection<function_types::int_alias (int), std::allocator<boost::poly_collection::detail::callable_wrapper<function_types::int_alias (int)> > >, ValueFactory = test_utilities::auto_increment, Types = <function_types::function1, function_types::function2, function_types::function3, function_types::function4, function_types::function5>]'  
../libs/poly_collection/test/test_construction.cpp(116): test 'p2.get_allocator().comes_from(Propagate?root1:root2)' failed in function 'void test_allocator_aware_construction() [Propagate = true, AlwaysEqual = false, PolyCollection = boost::poly_collection::function_collection<function_types::int_alias (int), std::allocator<boost::poly_collection::detail::callable_wrapper<function_types::int_alias (int)> > >, ValueFactory = test_utilities::auto_increment, Types = <function_types::function1, function_types::function2, function_types::function3, function_types::function4, function_types::function5>]'  
../libs/poly_collection/test/test_construction.cpp(116): test 'p2.get_allocator().comes_from(Propagate?root1:root2)' failed in function 'void test_allocator_aware_construction() [Propagate = true, AlwaysEqual = true, PolyCollection = boost::poly_collection::function_collection<function_types::int_alias (int), std::allocator<boost::poly_collection::detail::callable_wrapper<function_types::int_alias (int)> > >, ValueFactory = test_utilities::auto_increment, Types = <function_types::function1, function_types::function2, function_types::function3, function_types::function4, function_types::function5>]'  
../libs/poly_collection/test/test_construction.cpp(143): test 'p3.get_allocator().comes_from(Propagate?root1:root2)' failed in function 'void test_allocator_aware_construction() [Propagate = true, AlwaysEqual = true, PolyCollection = boost::poly_collection::function_collection<function_types::int_alias (int), std::allocator<boost::poly_collection::detail::callable_wrapper<function_types::int_alias (int)> > >, ValueFactory = test_utilities::auto_increment, Types = <function_types::function1, function_types::function2, function_types::function3, function_types::function4, function_types::function5>]'  
../libs/poly_collection/test/test_construction.cpp(179): test 'p2.get_allocator().comes_from(Propagate?root2:root1)' failed in function 'void test_allocator_aware_construction() [Propagate = true, AlwaysEqual = true, PolyCollection = boost::poly_collection::function_collection<function_types::int_alias (int), std::allocator<boost::poly_collection::detail::callable_wrapper<function_types::int_alias (int)> > >, ValueFactory = test_utilities::auto_increment, Types = <function_types::function1, function_types::function2, function_types::function3, function_types::function4, function_types::function5>]'  
../libs/poly_collection/test/test_construction.cpp(180): test 'p3.get_allocator().comes_from(Propagate?root1:root2)' failed in function 'void test_allocator_aware_construction() [Propagate = true, AlwaysEqual = true, PolyCollection = boost::poly_collection::function_collection<function_types::int_alias (int), std::allocator<boost::poly_collection::detail::callable_wrapper<function_types::int_alias (int)> > >, ValueFactory = test_utilities::auto_increment, Types = <function_types::function1, function_types::function2, function_types::function3, function_types::function4, function_types::function5>]'  
18 errors detected.`

---

## Comment 1

> Username: joaquintides  
> Created at: 2019-08-21 18:09:06 UTC  
> Url: https://github.com/boostorg/poly_collection/issues/16#issuecomment-523584231  

What compiler are you testing this in? Visual Studio's std library (derived from Dinkumware's) goes only up to `_CPPLIB_VER`  (of which `BOOST_DINKUMWARE_STDLIB` is an alias) = 650, so this 804 value is quite shocking.  
  
Also, looks like you're not using the most recent version of Boost.PolyCollection (there's no possibly failing test located at `test_construction.cpp(97)`). Care to rerun with Boost 1.71.0?

---

## Comment 2

> Username: BinCaoWR  
> Created at: 2019-08-23 06:11:44 UTC  
> Url: https://github.com/boostorg/poly_collection/issues/16#issuecomment-524186260  

I'm using gcc-8.1.0. I'm using the std library from Dinkumware(not Visual Studio), the version is V8.04x. My testing was run with Boost 1.70.0. I will try to rerun with Boost 1.71.0.

---

## Comment 3

> Username: joaquintides  
> Created at: 2019-08-27 11:57:07 UTC  
> Url: https://github.com/boostorg/poly_collection/issues/16#issuecomment-525268130  

Have you had the opportunity to rerun with Boost 1.71.0?

---

## Comment 4

> Username: BinCaoWR  
> Created at: 2019-08-30 02:38:21 UTC  
> Url: https://github.com/boostorg/poly_collection/issues/16#issuecomment-526432816  

Not yet. We have some local changes have to be re-patched to boost 1.71.0.

---

## Comment 5

> Username: BinCaoWR  
> Created at: 2019-09-03 02:16:41 UTC  
> Url: https://github.com/boostorg/poly_collection/issues/16#issuecomment-527278062  

It is still failed on boost 1.71.0.  
`../libs/poly_collection/test/test_construction.cpp(104): test 'p3.get_allocator().comes_from(root2)' failed in function 'void test_allocator_aware_construction() [with bool Propagate = true; bool AlwaysEqual = false; PolyCollection = boost::poly_collection::any_collection<boost::type_erasure::incrementable<> >; ValueFactory = test_utilities::auto_increment; Types = {any_types::incrementable1, double, any_types::incrementable3, int, boost::type_erasure::any<boost::mpl::vector4<boost::type_erasure::copy_constructible<boost::type_erasure::_self>, boost::type_erasure::assignable<boost::type_erasure::_self, const boost::type_erasure::_self&>, boost::type_erasure::incrementable<boost::type_erasure::_self>, any_types::convertible_to_int<boost::type_erasure::_self> >, boost::type_erasure::_self>}]'  
../libs/poly_collection/test/test_construction.cpp(123): test 'p2.get_allocator().comes_from(Propagate?root1:root2)' failed in function 'void test_allocator_aware_construction() [with bool Propagate = true; bool AlwaysEqual = false; PolyCollection = boost::poly_collection::any_collection<boost::type_erasure::incrementable<> >; ValueFactory = test_utilities::auto_increment; Types = {any_types::incrementable1, double, any_types::incrementable3, int, boost::type_erasure::any<boost::mpl::vector4<boost::type_erasure::copy_constructible<boost::type_erasure::_self>, boost::type_erasure::assignable<boost::type_erasure::_self, const boost::type_erasure::_self&>, boost::type_erasure::incrementable<boost::type_erasure::_self>, any_types::convertible_to_int<boost::type_erasure::_self> >, boost::type_erasure::_self>}]'  
../libs/poly_collection/test/test_construction.cpp(123): test 'p2.get_allocator().comes_from(Propagate?root1:root2)' failed in function 'void test_allocator_aware_construction() [with bool Propagate = true; bool AlwaysEqual = true; PolyCollection = boost::poly_collection::any_collection<boost::type_erasure::incrementable<> >; ValueFactory = test_utilities::auto_increment; Types = {any_types::incrementable1, double, any_types::incrementable3, int, boost::type_erasure::any<boost::mpl::vector4<boost::type_erasure::copy_constructible<boost::type_erasure::_self>, boost::type_erasure::assignable<boost::type_erasure::_self, const boost::type_erasure::_self&>, boost::type_erasure::incrementable<boost::type_erasure::_self>, any_types::convertible_to_int<boost::type_erasure::_self> >, boost::type_erasure::_self>}]'  
../libs/poly_collection/test/test_construction.cpp(150): test 'p3.get_allocator().comes_from(Propagate?root1:root2)' failed in function 'void test_allocator_aware_construction() [with bool Propagate = true; bool AlwaysEqual = true; PolyCollection = boost::poly_collection::any_collection<boost::type_erasure::incrementable<> >; ValueFactory = test_utilities::auto_increment; Types = {any_types::incrementable1, double, any_types::incrementable3, int, boost::type_erasure::any<boost::mpl::vector4<boost::type_erasure::copy_constructible<boost::type_erasure::_self>, boost::type_erasure::assignable<boost::type_erasure::_self, const boost::type_erasure::_self&>, boost::type_erasure::incrementable<boost::type_erasure::_self>, any_types::convertible_to_int<boost::type_erasure::_self> >, boost::type_erasure::_self>}]'  
../libs/poly_collection/test/test_construction.cpp(186): test 'p2.get_allocator().comes_from(Propagate?root2:root1)' failed in function 'void test_allocator_aware_construction() [with bool Propagate = true; bool AlwaysEqual = true; PolyCollection = boost::poly_collection::any_collection<boost::type_erasure::incrementable<> >; ValueFactory = test_utilities::auto_increment; Types = {any_types::incrementable1, double, any_types::incrementable3, int, boost::type_erasure::any<boost::mpl::vector4<boost::type_erasure::copy_constructible<boost::type_erasure::_self>, boost::type_erasure::assignable<boost::type_erasure::_self, const boost::type_erasure::_self&>, boost::type_erasure::incrementable<boost::type_erasure::_self>, any_types::convertible_to_int<boost::type_erasure::_self> >, boost::type_erasure::_self>}]'  
../libs/poly_collection/test/test_construction.cpp(187): test 'p3.get_allocator().comes_from(Propagate?root1:root2)' failed in function 'void test_allocator_aware_construction() [with bool Propagate = true; bool AlwaysEqual = true; PolyCollection = boost::poly_collection::any_collection<boost::type_erasure::incrementable<> >; ValueFactory = test_utilities::auto_increment; Types = {any_types::incrementable1, double, any_types::incrementable3, int, boost::type_erasure::any<boost::mpl::vector4<boost::type_erasure::copy_constructible<boost::type_erasure::_self>, boost::type_erasure::assignable<boost::type_erasure::_self, const boost::type_erasure::_self&>, boost::type_erasure::incrementable<boost::type_erasure::_self>, any_types::convertible_to_int<boost::type_erasure::_self> >, boost::type_erasure::_self>}]'  
../libs/poly_collection/test/test_construction.cpp(104): test 'p3.get_allocator().comes_from(root2)' failed in function 'void test_allocator_aware_construction() [with bool Propagate = true; bool AlwaysEqual = false; PolyCollection = boost::poly_collection::base_collection<base_types::base>; ValueFactory = test_utilities::auto_increment; Types = {base_types::derived1, base_types::derived2, base_types::derived3, base_types::derived4, base_types::derived5}]'  
../libs/poly_collection/test/test_construction.cpp(123): test 'p2.get_allocator().comes_from(Propagate?root1:root2)' failed in function 'void test_allocator_aware_construction() [with bool Propagate = true; bool AlwaysEqual = false; PolyCollection = boost::poly_collection::base_collection<base_types::base>; ValueFactory = test_utilities::auto_increment; Types = {base_types::derived1, base_types::derived2, base_types::derived3, base_types::derived4, base_types::derived5}]'  
../libs/poly_collection/test/test_construction.cpp(123): test 'p2.get_allocator().comes_from(Propagate?root1:root2)' failed in function 'void test_allocator_aware_construction() [with bool Propagate = true; bool AlwaysEqual = true; PolyCollection = boost::poly_collection::base_collection<base_types::base>; ValueFactory = test_utilities::auto_increment; Types = {base_types::derived1, base_types::derived2, base_types::derived3, base_types::derived4, base_types::derived5}]'  
../libs/poly_collection/test/test_construction.cpp(150): test 'p3.get_allocator().comes_from(Propagate?root1:root2)' failed in function 'void test_allocator_aware_construction() [with bool Propagate = true; bool AlwaysEqual = true; PolyCollection = boost::poly_collection::base_collection<base_types::base>; ValueFactory = test_utilities::auto_increment; Types = {base_types::derived1, base_types::derived2, base_types::derived3, base_types::derived4, base_types::derived5}]'  
../libs/poly_collection/test/test_construction.cpp(186): test 'p2.get_allocator().comes_from(Propagate?root2:root1)' failed in function 'void test_allocator_aware_construction() [with bool Propagate = true; bool AlwaysEqual = true; PolyCollection = boost::poly_collection::base_collection<base_types::base>; ValueFactory = test_utilities::auto_increment; Types = {base_types::derived1, base_types::derived2, base_types::derived3, base_types::derived4, base_types::derived5}]'  
../libs/poly_collection/test/test_construction.cpp(187): test 'p3.get_allocator().comes_from(Propagate?root1:root2)' failed in function 'void test_allocator_aware_construction() [with bool Propagate = true; bool AlwaysEqual = true; PolyCollection = boost::poly_collection::base_collection<base_types::base>; ValueFactory = test_utilities::auto_increment; Types = {base_types::derived1, base_types::derived2, base_types::derived3, base_types::derived4, base_types::derived5}]'  
../libs/poly_collection/test/test_construction.cpp(104): test 'p3.get_allocator().comes_from(root2)' failed in function 'void test_allocator_aware_construction() [with bool Propagate = true; bool AlwaysEqual = false; PolyCollection = boost::poly_collection::function_collection<function_types::int_alias(int)>; ValueFactory = test_utilities::auto_increment; Types = {function_types::function1, function_types::function2, function_types::function3, function_types::function4, function_types::function5}]'  
../libs/poly_collection/test/test_construction.cpp(123): test 'p2.get_allocator().comes_from(Propagate?root1:root2)' failed in function 'void test_allocator_aware_construction() [with bool Propagate = true; bool AlwaysEqual = false; PolyCollection = boost::poly_collection::function_collection<function_types::int_alias(int)>; ValueFactory = test_utilities::auto_increment; Types = {function_types::function1, function_types::function2, function_types::function3, function_types::function4, function_types::function5}]'  
../libs/poly_collection/test/test_construction.cpp(123): test 'p2.get_allocator().comes_from(Propagate?root1:root2)' failed in function 'void test_allocator_aware_construction() [with bool Propagate = true; bool AlwaysEqual = true; PolyCollection = boost::poly_collection::function_collection<function_types::int_alias(int)>; ValueFactory = test_utilities::auto_increment; Types = {function_types::function1, function_types::function2, function_types::function3, function_types::function4, function_types::function5}]'  
../libs/poly_collection/test/test_construction.cpp(150): test 'p3.get_allocator().comes_from(Propagate?root1:root2)' failed in function 'void test_allocator_aware_construction() [with bool Propagate = true; bool AlwaysEqual = true; PolyCollection = boost::poly_collection::function_collection<function_types::int_alias(int)>; ValueFactory = test_utilities::auto_increment; Types = {function_types::function1, function_types::function2, function_types::function3, function_types::function4, function_types::function5}]'  
../libs/poly_collection/test/test_construction.cpp(186): test 'p2.get_allocator().comes_from(Propagate?root2:root1)' failed in function 'void test_allocator_aware_construction() [with bool Propagate = true; bool AlwaysEqual = true; PolyCollection = boost::poly_collection::function_collection<function_types::int_alias(int)>; ValueFactory = test_utilities::auto_increment; Types = {function_types::function1, function_types::function2, function_types::function3, function_types::function4, function_types::function5}]'  
../libs/poly_collection/test/test_construction.cpp(187): test 'p3.get_allocator().comes_from(Propagate?root1:root2)' failed in function 'void test_allocator_aware_construction() [with bool Propagate = true; bool AlwaysEqual = true; PolyCollection = boost::poly_collection::function_collection<function_types::int_alias(int)>; ValueFactory = test_utilities::auto_increment; Types = {function_types::function1, function_types::function2, function_types::function3, function_types::function4, function_types::function5}]'  
18 errors detected.`

---

## Comment 6

> Username: joaquintides  
> Created at: 2019-09-03 08:44:30 UTC  
> Url: https://github.com/boostorg/poly_collection/issues/16#issuecomment-527365148  

Hi, could you check whether e6fa69fa2598087e6b2fdf78c28b6db10dc31735 clears the errors?

---

## Comment 7

> Username: joaquintides  
> Created at: 2019-09-03 12:40:52 UTC  
> Url: https://github.com/boostorg/poly_collection/issues/16#issuecomment-527440592  

I refined the commit, please use 40cd7e37147a0cc5d1196a0eff9552ee96f1d811 as well.

---

## Comment 8

> Username: BinCaoWR  
> Created at: 2019-09-04 07:23:32 UTC  
> Url: https://github.com/boostorg/poly_collection/issues/16#issuecomment-527777627  

Thanks for the quick fix. It passed with your changes.

---

## Comment 9

> Username: joaquintides  
> Created at: 2019-09-04 07:32:01 UTC  
> Url: https://github.com/boostorg/poly_collection/issues/16#issuecomment-527780179  

OK, closing then.  
  
If you don't mind, a year from now I'd like to reach back to you in order to run tests with `BOOST_DETECT_OUTDATED_WORKAROUNDS` to determine if newer versions of Dinkumware stdlib still require the workarounds.

---

## Comment 10

> Username: BinCaoWR  
> Created at: 2019-09-04 07:36:11 UTC  
> Url: https://github.com/boostorg/poly_collection/issues/16#issuecomment-527781525  

Sure. Thanks again.

---

## Comment 11

> Username: joaquintides  
> Created at: 2020-09-01 09:49:23 UTC  
> Url: https://github.com/boostorg/poly_collection/issues/16#issuecomment-684685895  

Hi, a year has passed since we closed this and I'd appreciate if you could get me some new info:  
* What is the value of `BOOST_DINKUMWARE_STDLIB` in your current environment? (It used to be 804.)  
* If this value has changed, would you mind re-running tests with the macro `BOOST_DETECT_OUTDATED_WORKAROUNDS` globally defined? This will tell us if the underlying Dinkumware problem has been fixed.

---

## Comment 12

> Username: calvincramer  
> Created at: 2020-09-02 17:38:07 UTC  
> Url: https://github.com/boostorg/poly_collection/issues/16#issuecomment-685891327  

Hi there, `BOOST_DINKUMWARE_STDLIB` is still `804`, and `test_construction` passes on all of the architectures we test for.

---

## Comment 13

> Username: joaquintides  
> Created at: 2020-09-03 06:50:57 UTC  
> Url: https://github.com/boostorg/poly_collection/issues/16#issuecomment-686292706  

OK, so things stay the same. I'll be back in a year to see if something's changed. Thank you!

---

## Comment 14

> Username: joaquintides  
> Created at: 2021-09-04 09:12:47 UTC  
> Url: https://github.com/boostorg/poly_collection/issues/16#issuecomment-912940009  

Hi, a year has passed and I'd like to ask again to see if things have changed:  
* What is the value of `BOOST_DINKUMWARE_STDLIB` in your current environment? (It used to be 804.)  
* If this value has changed, would you mind re-running tests with the macro `BOOST_DETECT_OUTDATED_WORKAROUNDS` globally defined? This will tell us if the underlying Dinkumware problem has been fixed.

---

## Comment 15

> Username: calvincramer  
> Created at: 2021-09-07 16:12:38 UTC  
> Url: https://github.com/boostorg/poly_collection/issues/16#issuecomment-914440061  

@joaquintides `BOOST_DINKUMWARE_STDLIB` is still `804`, so no change.

---

## Comment 16

> Username: joaquintides  
> Created at: 2021-09-08 07:07:31 UTC  
> Url: https://github.com/boostorg/poly_collection/issues/16#issuecomment-914977951  

Ok, looks like this a pretty stable library. Will ask back in a year if you don't mind.

---

## Comment 17

> Username: calvincramer  
> Created at: 2021-09-08 16:05:51 UTC  
> Url: https://github.com/boostorg/poly_collection/issues/16#issuecomment-915370514  

Please do!

---

## Comment 18

> Username: joaquintides  
> Created at: 2022-09-07 18:49:54 UTC  
> Url: https://github.com/boostorg/poly_collection/issues/16#issuecomment-1239757184  

Hi, a year has passed and I'd like to ask again to see if things have changed:  
* What is the value of `BOOST_DINKUMWARE_STDLIB` in your current environment? (It used to be 804.)  
* If this value has changed, would you mind re-running tests with the macro `BOOST_DETECT_OUTDATED_WORKAROUNDS` globally defined? This will tell us if the underlying Dinkumware problem has been fixed.
