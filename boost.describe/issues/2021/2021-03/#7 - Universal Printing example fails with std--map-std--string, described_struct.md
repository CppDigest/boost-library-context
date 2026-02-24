# #7 - Universal Printing example fails with std::map<std::string, described_struct> [Closed]

> Username: ddevienne  
> Created at: 2021-03-05 18:22:02 UTC  
> Updated at: 2021-03-17 13:26:41 UTC  
> Closed at: 2021-03-17 13:26:41 UTC  
> Url: https://github.com/boostorg/describe/issues/7  

There is no op<< for std::map. The universal printing example requires one.  
  
Trouble is, Boost.Describe seems to require it in the std namespace, except of course that's illegal to add to std,  
except for specializations. (I'm no expect, but I know that one). I cannot share the exact code that fails for me,  
since it's part of a large code-generated library, but essentially it boils down to what's below.  
  
With it's illegal op<< for the instantiated map template for <string, My::Inner>, this builds fine on MSVC 2019, C++17.  
I did not even try providing a templated one, to simply my testing.  
  
I realize it's not the best of report, but hopefully you can see the big picture, and perhaps even prove me wrong via a simple example.  
  
```  
#include <boost/describe.hpp>  
#include <boost/mp11.hpp>  
#include <boost/test/unit_test.hpp>  
  
#include <ostream>  
#include <sstream>  
  
namespace My {  
  
struct PseudoVariant { // not described  
  int idx_;  
  std::any val_;  
  // idx() getter, bunch of set_Xyz and get_Xyz() typed accessor to val_'s allowed content...  
};  
struct Inner { // described  
  PseudoVariant item_;  
};  
struct Outer { // described  
  std::map<std::string, Inner> inners_;  
  // other fields  
};  
  
// Dummy op<<(PseudoVariant) for test_2level_struct() specifically, printing just ints, not the other types  
std::ostream& operator<<(std::ostream& os, const PseudoVariant & dv_item) {  
    return os  
        << "{" << "#" << dv_item.idx() << "="  
        << ((dv_item.idx() == 2)? dv_item.get_int(): -1) << "}"  
    ;  
}  
  
} // My  
  
namespace std {  
  
std::ostream& operator<<(std::ostream& os, const std::map<std::string, My::Inner>& map) {  
    return os << My::to_string(map); // implemented elsewhere  
}  
  
} // std  
  
namespace describe_utils {  
  
using namespace boost::describe;  
  
std::ostream& operator<<(std::ostream& os, const T& t) {  
    os << '{';  
    bool first = true;  
    boost::mp11::mp_for_each<Bd>(  
        [&](auto D) {  
            if (!first) {  
                os << ", ";  
            }  
            first = false;  
  
            using B = typename decltype(D)::type;  
            os << (B const&)t;  
        }  
    );  
    boost::mp11::mp_for_each<Md>(  
        [&](auto D) {  
            if (!first ) {  
                os << ", ";  
            }  
            first = false;  
  
            os << '.' << D.name << " = " << t.*D.pointer;  
        }  
    );  
    return os << '}';  
}  
  
} // namespace describe_utils  
  
namespace {  
  
template <typename T>  
std::string as_string(const T& t) {  
    using namespace describe_utils;  
    std::ostringstream oss;  
    oss << t;  
    return oss.str();  
}  
  
BOOST_AUTO_TEST_SUITE(DescribeTests)  
  
BOOST_AUTO_TEST_CASE(test_2level_struct) {  
    using namespace describe_utils;  
    using namespace My;  
  
    Outer o;  
    // fill o  
  
    std::string sps = as_string(sp);  
}  
  
BOOST_AUTO_TEST_SUITE_END()  
  
} // End anonymous namespace  
  
```  
  
Here's the error I get, if not using that illegal std op<<.  
I put it in a different namespace, like the local anon one, or another _in-scope_, similar error each time.  
```  
1>------ Build started: Project: tests_describe, Configuration: Debug x64 ------  
1>DescribeTests.cpp  
1>D:\dev\src\tests\misc\DescribeTests.cpp(104,1): error C2679: binary '<<': no operator found which takes a right-hand operand of type 'const std::map<std::string,My::Inner,std::less<std::string>,std::allocator<std::pair<const std::string,My::Inner>>>' (or there is no acceptable conversion)  
1>C:\Program Files (x86)\Microsoft Visual Studio\2019\Professional\VC\Tools\MSVC\14.27.29110\include\ostream(466,39): message : could be 'std::basic_ostream<char,std::char_traits<char>> &std::basic_ostream<char,std::char_traits<char>>::operator <<(std::basic_streambuf<char,std::char_traits<char>> *)'  
1>C:\Program Files (x86)\Microsoft Visual Studio\2019\Professional\VC\Tools\MSVC\14.27.29110\include\ostream(441,39): message : or       'std::basic_ostream<char,std::char_traits<char>> &std::basic_ostream<char,std::char_traits<char>>::operator <<(const void *)'  
1>C:\Program Files (x86)\Microsoft Visual Studio\2019\Professional\VC\Tools\MSVC\14.27.29110\include\ostream(423,39): message : or       'std::basic_ostream<char,std::char_traits<char>> &std::basic_ostream<char,std::char_traits<char>>::operator <<(long double)'  
1>C:\Program Files (x86)\Microsoft Visual Studio\2019\Professional\VC\Tools\MSVC\14.27.29110\include\ostream(405,39): message : or       'std::basic_ostream<char,std::char_traits<char>> &std::basic_ostream<char,std::char_traits<char>>::operator <<(double)'  
1>C:\Program Files (x86)\Microsoft Visual Studio\2019\Professional\VC\Tools\MSVC\14.27.29110\include\ostream(387,39): message : or       'std::basic_ostream<char,std::char_traits<char>> &std::basic_ostream<char,std::char_traits<char>>::operator <<(float)'  
1>C:\Program Files (x86)\Microsoft Visual Studio\2019\Professional\VC\Tools\MSVC\14.27.29110\include\ostream(369,39): message : or       'std::basic_ostream<char,std::char_traits<char>> &std::basic_ostream<char,std::char_traits<char>>::operator <<(unsigned __int64)'  
1>C:\Program Files (x86)\Microsoft Visual Studio\2019\Professional\VC\Tools\MSVC\14.27.29110\include\ostream(351,39): message : or       'std::basic_ostream<char,std::char_traits<char>> &std::basic_ostream<char,std::char_traits<char>>::operator <<(__int64)'  
1>C:\Program Files (x86)\Microsoft Visual Studio\2019\Professional\VC\Tools\MSVC\14.27.29110\include\ostream(333,39): message : or       'std::basic_ostream<char,std::char_traits<char>> &std::basic_ostream<char,std::char_traits<char>>::operator <<(unsigned long)'  
1>C:\Program Files (x86)\Microsoft Visual Studio\2019\Professional\VC\Tools\MSVC\14.27.29110\include\ostream(315,39): message : or       'std::basic_ostream<char,std::char_traits<char>> &std::basic_ostream<char,std::char_traits<char>>::operator <<(long)'  
1>C:\Program Files (x86)\Microsoft Visual Studio\2019\Professional\VC\Tools\MSVC\14.27.29110\include\ostream(296,39): message : or       'std::basic_ostream<char,std::char_traits<char>> &std::basic_ostream<char,std::char_traits<char>>::operator <<(unsigned int)'  
1>C:\Program Files (x86)\Microsoft Visual Studio\2019\Professional\VC\Tools\MSVC\14.27.29110\include\ostream(270,39): message : or       'std::basic_ostream<char,std::char_traits<char>> &std::basic_ostream<char,std::char_traits<char>>::operator <<(int)'  
1>C:\Program Files (x86)\Microsoft Visual Studio\2019\Professional\VC\Tools\MSVC\14.27.29110\include\ostream(251,39): message : or       'std::basic_ostream<char,std::char_traits<char>> &std::basic_ostream<char,std::char_traits<char>>::operator <<(unsigned short)'  
1>C:\Program Files (x86)\Microsoft Visual Studio\2019\Professional\VC\Tools\MSVC\14.27.29110\include\ostream(217,39): message : or       'std::basic_ostream<char,std::char_traits<char>> &std::basic_ostream<char,std::char_traits<char>>::operator <<(short)'  
1>C:\Program Files (x86)\Microsoft Visual Studio\2019\Professional\VC\Tools\MSVC\14.27.29110\include\ostream(199,39): message : or       'std::basic_ostream<char,std::char_traits<char>> &std::basic_ostream<char,std::char_traits<char>>::operator <<(bool)'  
1>C:\Program Files (x86)\Microsoft Visual Studio\2019\Professional\VC\Tools\MSVC\14.27.29110\include\ostream(194,39): message : or       'std::basic_ostream<char,std::char_traits<char>> &std::basic_ostream<char,std::char_traits<char>>::operator <<(std::ios_base &(__cdecl *)(std::ios_base &))'  
1>C:\Program Files (x86)\Microsoft Visual Studio\2019\Professional\VC\Tools\MSVC\14.27.29110\include\ostream(189,39): message : or       'std::basic_ostream<char,std::char_traits<char>> &std::basic_ostream<char,std::char_traits<char>>::operator <<(std::basic_ios<char,std::char_traits<char>> &(__cdecl *)(std::basic_ios<char,std::char_traits<char>> &))'  
1>C:\Program Files (x86)\Microsoft Visual Studio\2019\Professional\VC\Tools\MSVC\14.27.29110\include\ostream(184,39): message : or       'std::basic_ostream<char,std::char_traits<char>> &std::basic_ostream<char,std::char_traits<char>>::operator <<(std::basic_ostream<char,std::char_traits<char>> &(__cdecl *)(std::basic_ostream<char,std::char_traits<char>> &))'  
1>D:\dev\src\tests\misc\DescribeTests.cpp(31,15): message : or       'std::ostream &My::operator <<(std::ostream &,const My::PSeudoVariant &)' [found using argument-dependent lookup]  
1>C:\Program Files (x86)\Microsoft Visual Studio\2019\Professional\VC\Tools\MSVC\14.27.29110\include\ostream(461,20): message : or       'std::basic_ostream<char,std::char_traits<char>> &std::basic_ostream<char,std::char_traits<char>>::operator <<<void>(std::nullptr_t)'  
1>C:\Program Files (x86)\Microsoft Visual Studio\2019\Professional\VC\Tools\MSVC\14.27.29110\include\ostream(654,32): message : or       'std::basic_ostream<char,std::char_traits<char>> &std::operator <<<char,std::char_traits<char>>(std::basic_ostream<char,std::char_traits<char>> &,const char *)'  
1>C:\Program Files (x86)\Microsoft Visual Studio\2019\Professional\VC\Tools\MSVC\14.27.29110\include\ostream(698,32): message : or       'std::basic_ostream<char,std::char_traits<char>> &std::operator <<<char,std::char_traits<char>>(std::basic_ostream<char,std::char_traits<char>> &,char)'  
1>C:\Program Files (x86)\Microsoft Visual Studio\2019\Professional\VC\Tools\MSVC\14.27.29110\include\ostream(734,31): message : or       'std::basic_ostream<char,std::char_traits<char>> &std::operator <<<std::char_traits<char>>(std::basic_ostream<char,std::char_traits<char>> &,const char *)'  
1>C:\Program Files (x86)\Microsoft Visual Studio\2019\Professional\VC\Tools\MSVC\14.27.29110\include\ostream(779,31): message : or       'std::basic_ostream<char,std::char_traits<char>> &std::operator <<<std::char_traits<char>>(std::basic_ostream<char,std::char_traits<char>> &,char)'  
1>C:\Program Files (x86)\Microsoft Visual Studio\2019\Professional\VC\Tools\MSVC\14.27.29110\include\ostream(896,31): message : or       'std::basic_ostream<char,std::char_traits<char>> &std::operator <<<std::char_traits<char>>(std::basic_ostream<char,std::char_traits<char>> &,const signed char *)'  
1>C:\Program Files (x86)\Microsoft Visual Studio\2019\Professional\VC\Tools\MSVC\14.27.29110\include\ostream(902,31): message : or       'std::basic_ostream<char,std::char_traits<char>> &std::operator <<<std::char_traits<char>>(std::basic_ostream<char,std::char_traits<char>> &,signed char)'  
1>C:\Program Files (x86)\Microsoft Visual Studio\2019\Professional\VC\Tools\MSVC\14.27.29110\include\ostream(907,31): message : or       'std::basic_ostream<char,std::char_traits<char>> &std::operator <<<std::char_traits<char>>(std::basic_ostream<char,std::char_traits<char>> &,const unsigned char *)'  
1>C:\Program Files (x86)\Microsoft Visual Studio\2019\Professional\VC\Tools\MSVC\14.27.29110\include\ostream(913,31): message : or       'std::basic_ostream<char,std::char_traits<char>> &std::operator <<<std::char_traits<char>>(std::basic_ostream<char,std::char_traits<char>> &,unsigned char)'  
1>C:\Program Files (x86)\Microsoft Visual Studio\2019\Professional\VC\Tools\MSVC\14.27.29110\include\ostream(1001,32): message : or       'std::basic_ostream<char,std::char_traits<char>> &std::operator <<<char,std::char_traits<char>>(std::basic_ostream<char,std::char_traits<char>> &,const std::error_code &)'  
1>D:\dev\src\tests\misc\DescribeTests.cpp(104,1): message : while trying to match the argument list '(std::basic_ostream<char,std::char_traits<char>>, const std::map<std::string,My::Inner,std::less<std::string>,std::allocator<std::pair<const std::string,My::Inner>>>)'  
1>D:\pdgm\kits\trunk\boost\1.74.0\Win_x64_10_v16_debug\include\boost/mp11/algorithm.hpp(1037): message : see reference to function template instantiation 'auto describe_utils::<<::<lambda_734585ecef8cae37a0607e5d54dd865b>::operator ()<boost::describe::detail::member_descriptor<My::_public_member_descriptor_fn::<lambda_bab1101c997e04563d84b2536558ac70>::()::D,1>>(boost::describe::detail::member_descriptor<My::_public_member_descriptor_fn::<lambda_bab1101c997e04563d84b2536558ac70>::()::D,1>) const' being compiled  
1>D:\pdgm\kits\trunk\boost\1.74.0\Win_x64_10_v16_debug\include\boost/mp11/algorithm.hpp(1055): message : see reference to function template instantiation 'F boost::mp11::detail::mp_for_each_impl<boost::describe::detail::member_descriptor<My::_public_member_descriptor_fn::<lambda_88c58abe40ddca9e64b2955f6dbfa7ea>::()::D,1>,boost::describe::detail::member_descriptor<My::_public_member_descriptor_fn::<lambda_5916e84125bc784d483539ad1d3a3d09>::()::D,1>,boost::describe::detail::member_descriptor<My::_public_member_descriptor_fn::<lambda_c9d2335107088328dfd07a87925d597b>::()::D,1>,boost::describe::detail::member_descriptor<My::_public_member_descriptor_fn::<lambda_bab1101c997e04563d84b2536558ac70>::()::D,1>,_Ty>(boost::mp11::mp_list<boost::describe::detail::member_descriptor<My::_public_member_descriptor_fn::<lambda_88c58abe40ddca9e64b2955f6dbfa7ea>::()::D,1>,boost::describe::detail::member_descriptor<My::_public_member_descriptor_fn::<lambda_5916e84125bc784d483539ad1d3a3d09>::()::D,1>,boost::describe::detail::member_descriptor<My::_public_member_descriptor_fn::<lambda_c9d2335107088328dfd07a87925d597b>::()::D,1>,boost::describe::detail::member_descriptor<My::_public_member_descriptor_fn::<lambda_bab1101c997e04563d84b2536558ac70>::()::D,1>>,F &&)' being compiled  
1>        with  
1>        [  
1>            F=describe_utils::<<::<lambda_734585ecef8cae37a0607e5d54dd865b>,  
1>            _Ty=describe_utils::<<::<lambda_734585ecef8cae37a0607e5d54dd865b>  
1>        ]  
1>D:\dev\src\tests\misc\DescribeTests.cpp(106): message : see reference to function template instantiation 'describe_utils::<<::<lambda_734585ecef8cae37a0607e5d54dd865b> boost::mp11::mp_for_each<Md,describe_utils::<<::<lambda_734585ecef8cae37a0607e5d54dd865b>>(F &&)' being compiled  
1>        with  
1>        [  
1>            Md=boost::describe::detail::list<boost::describe::detail::member_descriptor<My::_public_member_descriptor_fn::<lambda_88c58abe40ddca9e64b2955f6dbfa7ea>::()::D,1>,boost::describe::detail::member_descriptor<My::_public_member_descriptor_fn::<lambda_5916e84125bc784d483539ad1d3a3d09>::()::D,1>,boost::describe::detail::member_descriptor<My::_public_member_descriptor_fn::<lambda_c9d2335107088328dfd07a87925d597b>::()::D,1>,boost::describe::detail::member_descriptor<My::_public_member_descriptor_fn::<lambda_bab1101c997e04563d84b2536558ac70>::()::D,1>>,  
1>            F=describe_utils::<<::<lambda_734585ecef8cae37a0607e5d54dd865b>  
1>        ]  
1>D:\dev\src\tests\misc\DescribeTests.cpp(118): message : see reference to function template instantiation 'std::ostream &describe_utils::operator <<<T,boost::describe::detail::list<>,boost::describe::detail::list<boost::describe::detail::member_descriptor<My::_public_member_descriptor_fn::<lambda_88c58abe40ddca9e64b2955f6dbfa7ea>::()::D,1>,boost::describe::detail::member_descriptor<My::_public_member_descriptor_fn::<lambda_5916e84125bc784d483539ad1d3a3d09>::()::D,1>,boost::describe::detail::member_descriptor<My::_public_member_descriptor_fn::<lambda_c9d2335107088328dfd07a87925d597b>::()::D,1>,boost::describe::detail::member_descriptor<My::_public_member_descriptor_fn::<lambda_bab1101c997e04563d84b2536558ac70>::()::D,1>>>(std::ostream &,const T &)' being compiled  
1>        with  
1>        [  
1>            T=My::Outer  
1>        ]  
1>D:\dev\src\tests\misc\DescribeTests.cpp(200): message : see reference to function template instantiation 'std::string `anonymous-namespace'::as_string<My::Outer>(const T &)' being compiled  
1>        with  
1>        [  
1>            T=My::Outer  
1>        ]  
1>Done building project "tests_describe.vcxproj" -- FAILED.  
========== Build: 0 succeeded, 1 failed, 0 up-to-date, 0 skipped ==========  
```

---

## Comment 1

> Username: pdimov  
> Created at: 2021-03-05 18:33:56 UTC  
> Url: https://github.com/boostorg/describe/issues/7#issuecomment-791604208  

That's not a Boost.Describe requirement, it's how C++ works; for op<< to be found it needs to be in an associated namespace of the class. It's not possible for users to legally define op<< for std::map (at least not in the general case) because they don't own the type; the standard library does.  
  
For `std::map<std::string, My::Inner>` you can provide it in namespace `My` and it should work.  
  
That's also the reason the library can't provide the universal print function; it also needs to go into namespace `My` in order to apply to described types in `My`, otherwise it won't be found.

---

## Comment 2

> Username: ddevienne  
> Created at: 2021-03-05 18:45:14 UTC  
> Url: https://github.com/boostorg/describe/issues/7#issuecomment-791609800  

I tried that, and it didn't work. But I may have funged it. I'll retry it later, possibly on a shorter example. As of now, I'm stuck.

---

## Comment 3

> Username: pdimov  
> Created at: 2021-03-05 18:56:37 UTC  
> Updated at: 2021-03-05 18:57:36 UTC  
> Url: https://github.com/boostorg/describe/issues/7#issuecomment-791615811  

I moved the << operators into `namespace My`, fixed the errors, and it worked: https://gist.github.com/pdimov/a10562499e346cda712472c3cdef2d47  
  
(that is, it compiled, didn't link because there's no definition of `to_string` for `map`, as it's "provided elsewhere".)

---

## Comment 4

> Username: ddevienne  
> Created at: 2021-03-05 21:36:37 UTC  
> Url: https://github.com/boostorg/describe/issues/7#issuecomment-791725420  

Thanks Peter! Problem is, the generated types are in different namespaces, so does that mean the utilities formerly in the describe_utils namespace need to be duplicated in the std::map|vector|etc... using class namespaces? That would also mean they cannot be provided generically by Boost.Describe itself, as several reviewers asked as well?  
  
BTW, I had trouble with op<<, but not with op==, on the same types. That compiled fine.  
So what's special about op<<, to require the describe-using templates and the described types to be in the same NS?

---

## Comment 5

> Username: pdimov  
> Created at: 2021-03-05 21:50:59 UTC  
> Url: https://github.com/boostorg/describe/issues/7#issuecomment-791733691  

> That would also mean they cannot be provided generically by Boost.Describe itself, as several reviewers asked as well?  
  
Yes, it's not possible to provide an operator generically. It must be in an associated namespace.  
  
> So what's special about op<<, to require the describe-using templates and the described types to be in the same NS?  
  
Nothing is special about op<<. op== should have the same problem. I don't know why it worked, but the general case is much the same. E.g. https://gist.github.com/pdimov/ed7b17497211aae9cdf55b4e04179ee9 fails when the generic op== is not in `namespace app`.

---

## Comment 6

> Username: ddevienne  
> Created at: 2021-03-17 13:26:41 UTC  
> Url: https://github.com/boostorg/describe/issues/7#issuecomment-801078599  

Closing this, as not really an issue with the library itself.
