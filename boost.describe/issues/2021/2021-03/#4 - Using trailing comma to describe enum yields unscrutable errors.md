# #4 - Using trailing comma to describe enum yields unscrutable errors [Closed]

> Username: ddevienne  
> Created at: 2021-03-04 18:39:36 UTC  
> Updated at: 2021-03-15 15:22:50 UTC  
> Closed at: 2021-03-15 15:22:50 UTC  
> Url: https://github.com/boostorg/describe/issues/4  

My enums are code-generated, and the last enumerator has a trailing comma, which is OK in C++11.  
But when I copy/pasted it to turn it into a `BOOST_DESCRIBE_ENUM`, this yielding weird errors like below.  
Removing the trailing comma fixed the problem, but I wasted quite a bit of time on this.  
  
Any way to _fix_ this, so it works with trailing commas, as enums do?  
Or at least tweak the code to yield a better error message?  
  
```  
1>DescribeTests.cpp  
1>D:\boost/describe/enum.hpp(29,1): error C2182: 'value': illegal use of type 'void'  
1>D:\boost\1.74.0\Win_x64_10_v16_debug\include\boost/mp11/algorithm.hpp(1037): message : see reference to class template instantiation 'boost::describe::detail::enum_descriptor<Foo::_enum_descriptor_fn::<lambda_9250c7397ae982f1d2ae378dbdd22917>::()::D>' being compiled  
1>D:\boost\1.74.0\Win_x64_10_v16_debug\include\boost/mp11/algorithm.hpp(1055): message : see reference to function template instantiation 'F boost::mp11::detail::mp_for_each_impl<boost::describe::detail::enum_descriptor<Foo::_enum_descriptor_fn::<lambda_22d39ba0fa5275074785db73206bfcec>::()::D>,boost::describe::detail::enum_descriptor<Foo::_enum_descriptor_fn::<lambda_b36e895947c870ba551a0beff49d6047>::()::D>,boost::describe::detail::enum_descriptor<Foo::_enum_descriptor_fn::<lambda_294ffb3a79474efc9e18b549fb7261d1>::()::D>,boost::describe::detail::enum_descriptor<Foo::_enum_descriptor_fn::<lambda_9250c7397ae982f1d2ae378dbdd22917>::()::D>,_Ty>(boost::mp11::mp_list<boost::describe::detail::enum_descriptor<Foo::_enum_descriptor_fn::<lambda_22d39ba0fa5275074785db73206bfcec>::()::D>,boost::describe::detail::enum_descriptor<Foo::_enum_descriptor_fn::<lambda_b36e895947c870ba551a0beff49d6047>::()::D>,boost::describe::detail::enum_descriptor<Foo::_enum_descriptor_fn::<lambda_294ffb3a79474efc9e18b549fb7261d1>::()::D>,boost::describe::detail::enum_descriptor<Foo::_enum_descriptor_fn::<lambda_9250c7397ae982f1d2ae378dbdd22917>::()::D>>,F &&)' being compiled  
1>        with  
1>        [  
1>            F=`anonymous-namespace'::enum_to_string::<lambda_35eeefbe1e328ec36c865ccd2344c156>,  
1>            _Ty=`anonymous-namespace'::enum_to_string::<lambda_35eeefbe1e328ec36c865ccd2344c156>  
1>        ]  
1>D:\pdgm\trunk\psc3\SharedComponents\src\tests\eml\DescribeTests.cpp(26): message : see reference to function template instantiation '`anonymous-namespace'::enum_to_string::<lambda_35eeefbe1e328ec36c865ccd2344c156> boost::mp11::mp_for_each<boost::describe::detail::list<boost::describe::detail::enum_descriptor<Foo::_enum_descriptor_fn::<lambda_22d39ba0fa5275074785db73206bfcec>::()::D>,boost::describe::detail::enum_descriptor<Foo::_enum_descriptor_fn::<lambda_b36e895947c870ba551a0beff49d6047>::()::D>,boost::describe::detail::enum_descriptor<Foo::_enum_descriptor_fn::<lambda_294ffb3a79474efc9e18b549fb7261d1>::()::D>,boost::describe::detail::enum_descriptor<Foo::_enum_descriptor_fn::<lambda_9250c7397ae982f1d2ae378dbdd22917>::()::D>>,`anonymous-namespace'::enum_to_string::<lambda_35eeefbe1e328ec36c865ccd2344c156>>(F &&)' being compiled  
1>        with  
1>        [  
1>            F=`anonymous-namespace'::enum_to_string::<lambda_35eeefbe1e328ec36c865ccd2344c156>  
1>        ]  
1>D:\pdgm\trunk\psc3\SharedComponents\src\tests\eml\DescribeTests.cpp(42): message : see reference to function template instantiation 'const char *`anonymous-namespace'::enum_to_string<Foo::Bar>(E)' being compiled  
1>        with  
1>        [  
1>            E=Foo::Bar  
1>        ]  
1>D:\boost/describe/enum.hpp(29,1): error C2440: 'initializing': cannot convert from 'void' to 'const int'  
1>D:\boost/describe/enum.hpp(29,59): message : Expressions of type void cannot be converted to other types  
1>Done building project "tests_describe.vcxproj" -- FAILED.  
========== Build: 0 succeeded, 1 failed, 0 up-to-date, 0 skipped ==========  
```

---

## Comment 1

> Username: pdimov  
> Created at: 2021-03-04 19:06:17 UTC  
> Url: https://github.com/boostorg/describe/issues/4#issuecomment-790856648  

Can you please post the exact code?

---

## Comment 2

> Username: ddevienne  
> Created at: 2021-03-05 08:20:59 UTC  
> Url: https://github.com/boostorg/describe/issues/4#issuecomment-791248715  

I suspect adding a trailing comma to your own example would be enough, but here goes anyway.  
I tried a bunch of things before realizing the trailing-comma mistake I made, w/ or w/o namespaces,  
separate TUs for the def and describe or not (former is my use case), longer names, etc... Thus the weird code.  
  
PS: it's not the same exact code I sent errors for, but fails the same way  
PPS: the test builds and passes when removing the trailing comma after `eCcdEfghIjklmop` on `BOOST_DESCRIBE_ENUM`  
  
```  
#include <boost/describe.hpp>  
#include <boost/mp11.hpp>  
#include <boost/test/unit_test.hpp>  
  
namespace Foo {  
  
enum Bar { eAbcdEfghIjklm, eBcdEfghIjklmo, eCcdEfghIjklmop, };  
  
BOOST_DESCRIBE_ENUM(Bar, eAbcdEfghIjklm, eBcdEfghIjklmo, eCcdEfghIjklmop,)  
  
}  
  
namespace {  
  
template<class E> char const * enum_to_string(E e) {  
    const char* r = "(unnamed)";  
  
    boost::mp11::mp_for_each<boost::describe::describe_enumerators<E>>(  
        [&](auto D) {  
            if (e == D.value) {  
                r = D.name;  
            }  
        }  
    );  
    return r;  
}  
  
BOOST_AUTO_TEST_SUITE(DescribeTests)  
  
BOOST_AUTO_TEST_CASE(test_enum) {  
    using namespace Foo;  
    Bar bar = eCcdEfghIjklmop;  
    std::string bar_name = enum_to_string(bar);  
    BOOST_CHECK_EQUAL(bar_name, "eCcdEfghIjklmop");  
}  
  
BOOST_AUTO_TEST_SUITE_END()  
  
}  
```  
  
Microsoft Visual Studio Professional 2019, Version 16.7.6  
Visual C++ 2019   00435-30010-36205-AA935  
In C++17 mode.

---

## Comment 3

> Username: ddevienne  
> Created at: 2021-03-05 08:24:16 UTC  
> Url: https://github.com/boostorg/describe/issues/4#issuecomment-791250721  

Oh, forgot to mention, I layered your Describe lib on top of Boost 1.74, which is my company's current version I can't upgrade.  
Doesn't seem to give any trouble. Please keep this in mind, we can't all upgrade to the latest and greatest, yet still want to use  
your great libraries Peter :)

---

## Comment 4

> Username: pdimov  
> Created at: 2021-03-15 15:22:50 UTC  
> Url: https://github.com/boostorg/describe/issues/4#issuecomment-799507154  

A trailing comma should now work (although it does cause a preprocessor warning on MSVC.)
