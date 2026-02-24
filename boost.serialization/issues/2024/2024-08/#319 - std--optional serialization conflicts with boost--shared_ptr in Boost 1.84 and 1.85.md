# #319 - std::optional serialization conflicts with boost::shared_ptr in Boost 1.84 and 1.85 [Open]

> Username: sowle  
> Created at: 2024-08-11 20:31:45 UTC  
> Updated at: 2024-09-23 22:59:26 UTC  
> Url: https://github.com/boostorg/serialization/issues/319  

I'm experiencing a very strange compilation issue when using boost::shared_ptr with the (de)serialization of std::optional.  
  
std::optional serialization support has been added recently in version 1.84.0: https://www.boost.org/doc/libs/1_84_0/boost/serialization/optional.hpp (see also commit [61a2b1207619cedb1f4d3ac1c7193f10ef9334f7](https://github.com/boostorg/serialization/commit/61a2b1207619cedb1f4d3ac1c7193f10ef9334f7))  
  
After a lot of effort I have reduced the problem to a minimal reproducible example, which highlights the issue **in both 1.84 and 1.85** versions (earlier versions don't support std::optional serialization).  
  
MRE:  
```  
#include <fstream>  
#include <iostream>  
#include <optional>  
#include <boost/filesystem/fstream.hpp>  
#include <boost/serialization/optional.hpp>  
#include <boost/archive/text_iarchive.hpp>  
#include <boost/archive/text_oarchive.hpp>  
  
struct pluto_t  
{  
  uint64_t mass = 0;  
  std::optional<uint64_t> optional_mass;  
  
  template <class archive_t>  
  void serialize(archive_t &_a, const unsigned int ver)  
  {  
    _a & mass;  
    _a & optional_mass;                          // <-- no error if this line is commented out  
  }  
  
};  
  
  
int main()  
{  
  const char* filename = "tmpfile";  
  // store to file  
  {  
    pluto_t pluto{};  
    pluto.mass = 13025000000000000000ull;  
  
    std::ofstream ofs(filename);  
    boost::archive::text_oarchive oa(ofs);  
    oa << pluto;  
  }  
  
  // load from file  
  {  
    pluto_t pluto{};  
  
    std::ifstream ifs(filename);  
    boost::archive::text_iarchive ia(ifs);  
    ia >> pluto;                                 // <-- no error if this line is commented out  
  
    std::cout << pluto.mass << std::endl;  
  }  
  
  std::cout << "done." << std::endl;  
  return 0;  
}  
  
  
#include <boost/serialization/shared_ptr.hpp>    // <-- no error if this line is commented out  
  
[[maybe_unused]] static int foo = 42;  
```  
  
Error (there's lots of text, here's only the error line):  
```  
/usr/include/c++/8/type_traits:1147:12: error: invalid use of incomplete type ‘class boost::serialization::U’  
     struct __is_trivially_copy_constructible_impl<_Tp, true>  
            ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
```  
  
Compilation flags:  
`g++ f.cpp -std=c++17 -g -O3 -Wall -DNDEBUG -o f -I$BOOST_ROOT -L$BOOST_ROOT/stage/lib -static -lboost_system -lboost_serialization -s`  
  
Compiler version:  
```  
g++ --version  
g++ (Ubuntu 8.4.0-1ubuntu1~18.04) 8.4.0  
Copyright (C) 2018 Free Software Foundation, Inc.  
```  
  
This example doesn't compile.  
If any of the corresponding lines in the code are commented out, there's no error whatsoever.  
  
UPD. It looks like a gcc-specific issue. I have no compilation issues with MSVC Community 2022 (64-bit) Version 17.10.4.

---

## Comment 1

> Username: mclow  
> Created at: 2024-08-11 22:42:20 UTC  
> Url: https://github.com/boostorg/serialization/issues/319#issuecomment-2282920703  

Compiles w/o error for me using clang:  `clang++ -std=c++17 -I $BOOST boost.cpp -c`

---

## Comment 2

> Username: correaa  
> Created at: 2024-08-12 00:09:06 UTC  
> Updated at: 2024-08-12 00:24:23 UTC  
> Url: https://github.com/boostorg/serialization/issues/319#issuecomment-2282937469  

Confirmed in Godbolt: https://godbolt.org/z/8h5Mc3aYe (works with clang and not with gcc).  
  
It is strange that is trying to use a type called `boost::serialization::U`; seems like a template parameter  `U` missing somewhere or interpreted as an actual type (maybe a `typename` missing somewhere).

---

## Comment 3

> Username: correaa  
> Created at: 2024-08-12 00:28:56 UTC  
> Url: https://github.com/boostorg/serialization/issues/319#issuecomment-2282949378  

This is the problematic code: https://github.com/boostorg/serialization/blob/develop/include/boost/serialization/shared_ptr_helper.hpp#L45  
  
Here it is a confirmation that this line is problematic and a proposed change by properly declaring U as a template parameter  https://godbolt.org/z/jh5T1Tvjs  
Not sure how this ever worked since it is rejected by both compilers.  
  
The difference in the two compilers may come from the `BOOST_NO_MEMBER_TEMPLATE_FRIENDS`, which, in any case, can give you accidentally a way to workaround the problem until it is fixed: https://godbolt.org/z/sxWPhfrr4

---

## Comment 4

> Username: correaa  
> Created at: 2024-08-14 16:33:45 UTC  
> Url: https://github.com/boostorg/serialization/issues/319#issuecomment-2289259274  

I opened a PR https://github.com/boostorg/serialization/pull/320 to solve what that I think causes this problem.

---

## Comment 5

> Username: olologin  
> Created at: 2024-08-15 16:55:13 UTC  
> Url: https://github.com/boostorg/serialization/issues/319#issuecomment-2291714451  

Yep, we found the same problem in our project today. Please fix :)

---

## Comment 6

> Username: olologin  
> Created at: 2024-08-16 09:04:02 UTC  
> Updated at: 2024-08-16 09:09:38 UTC  
> Url: https://github.com/boostorg/serialization/issues/319#issuecomment-2293126143  

@correaa   
  
Does the following sample work with your fix for you?  
  
```  
#include <optional>  
#include <sstream>  
  
#include <boost/archive/binary_iarchive.hpp>  
#include <boost/serialization/optional.hpp>  
#include <boost/serialization/shared_ptr.hpp>  
#include <boost/serialization/vector.hpp>  
///////////  
  
void Foo()  
{  
	std::istringstream archiveStream;  
	boost::archive::binary_iarchive arch(archiveStream);  
	auto asd = std::optional<size_t>();  
	auto bsd = std::vector<long unsigned int>();  
	arch & asd;  
	arch & bsd;  
}  
```  
  
  
I tried your fix, but for me it raises the following issue, but I must say beforehand - I am using 1.78 with some patches backported from more recent versions, so maybe it is my fault:  
```  
/builds/branch1/company/dev/third_party/boost_1_78/boost/serialization/split_free.hpp: In instantiation of ‘static void boost::serialization::free_loader<Archive, T>::invoke(Archive&, T&, unsigned int) [with Archive = boost::archive::binary_iarchive; T = std::vector<long unsigned int>]’:  
/builds/branch1/company/dev/third_party/boost_1_78/boost/serialization/split_free.hpp:74:18:   required from ‘void boost::serialization::split_free(Archive&, T&, unsigned int) [with Archive = boost::archive::binary_iarchive; T = std::vector<long unsigned int>]’  
/builds/branch1/company/dev/third_party/boost_1_78/boost/serialization/vector.hpp:177:39:   required from ‘void boost::serialization::serialize(Archive&, std::vector<_ValT, _Allocator>&, unsigned int) [with Archive = boost::archive::binary_iarchive; U = long unsigned int; Allocator = std::allocator<long unsigned int>]’  
/builds/branch1/company/dev/third_party/boost_1_78/boost/serialization/serialization.hpp:109:14:   required from ‘void boost::serialization::serialize_adl(Archive&, T&, unsigned int) [with Archive = boost::archive::binary_iarchive; T = std::vector<long unsigned int>]’  
/builds/branch1/company/dev/third_party/boost_1_78/boost/archive/detail/iserializer.hpp:189:42:   required from ‘void boost::archive::detail::iserializer<Archive, T>::load_object_data(boost::archive::detail::basic_iarchive&, void*, unsigned int) const [with Archive = boost::archive::binary_iarchive; T = std::vector<long unsigned int>]’  
/builds/branch1/company/dev/third_party/boost_1_78/boost/archive/detail/iserializer.hpp:122:7:   required from ‘class boost::archive::detail::iserializer<boost::archive::binary_iarchive, std::vector<long unsigned int> >’  
/builds/branch1/company/dev/third_party/boost_1_78/boost/archive/detail/iserializer.hpp:411:27:   [ skipping 4 instantiation contexts, use -ftemplate-backtrace-limit=0 to disable ]  
/builds/branch1/company/dev/third_party/boost_1_78/boost/archive/detail/common_iarchive.hpp:67:22:   required from ‘void boost::archive::detail::common_iarchive<Archive>::load_override(T&) [with T = std::vector<long unsigned int>; Archive = boost::archive::binary_iarchive]’  
/builds/branch1/company/dev/third_party/boost_1_78/boost/archive/basic_binary_iarchive.hpp:76:50:   required from ‘void boost::archive::basic_binary_iarchive<Archive>::load_override(T&) [with T = std::vector<long unsigned int>; Archive = boost::archive::binary_iarchive]’  
/builds/branch1/company/dev/third_party/boost_1_78/boost/archive/binary_iarchive_impl.hpp:58:60:   required from ‘void boost::archive::binary_iarchive_impl<Archive, Elem, Tr>::load_override(T&) [with T = std::vector<long unsigned int>; Archive = boost::archive::binary_iarchive; Elem = char; Tr = std::char_traits<char>]’  
/builds/branch1/company/dev/third_party/boost_1_78/boost/archive/detail/interface_iarchive.hpp:68:36:   required from ‘Archive& boost::archive::detail::interface_iarchive<Archive>::operator>>(T&) [with T = std::vector<long unsigned int>; Archive = boost::archive::binary_iarchive]’  
/builds/branch1/company/dev/third_party/boost_1_78/boost/archive/detail/interface_iarchive.hpp:75:32:   required from ‘Archive& boost::archive::detail::interface_iarchive<Archive>::operator&(T&) [with T = std::vector<long unsigned int>; Archive = boost::archive::binary_iarchive]’  
/builds/branch1/company/dev/libraries/TPVisualizer/src/test.cpp:17:9:   required from here  
/builds/branch1/company/dev/third_party/boost_1_78/boost/serialization/split_free.hpp:58:13: error: call of overloaded ‘load(boost::archive::binary_iarchive&, std::vector<long unsigned int>&, const boost::serialization::version_type&)’ is ambiguous  
   58 |         load(ar, t, v);  
      |         ~~~~^~~~~~~~~~  
In file included from /builds/branch1/company/dev/third_party/boost_1_78/boost/serialization/shared_ptr.hpp:29,  
                 from /builds/branch1/company/dev/libraries/TPVisualizer/src/test.cpp:6:  
/builds/branch1/company/dev/third_party/boost_1_78/boost/serialization/shared_ptr_helper.hpp:44:6: note: candidate: ‘void boost::serialization::load(Archive&, SPT<U>&, unsigned int) [with Archive = boost::archive::binary_iarchive; SPT = std::vector; U = long unsigned int]’  
   44 | void load(  
      |      ^~~~  
In file included from /builds/branch1/company/dev/libraries/TPVisualizer/src/test.cpp:7:  
/builds/branch1/company/dev/third_party/boost_1_78/boost/serialization/vector.hpp:150:13: note: candidate: ‘void boost::serialization::load(Archive&, std::vector<_ValT, _Allocator>&, unsigned int) [with Archive = boost::archive::binary_iarchive; U = long unsigned int; Allocator = std::allocator<long unsigned int>]’  
  150 | inline void load(  
```

---

## Comment 7

> Username: olologin  
> Created at: 2024-08-16 15:50:05 UTC  
> Updated at: 2024-08-16 15:53:54 UTC  
> Url: https://github.com/boostorg/serialization/issues/319#issuecomment-2293750661  

The following replacement of faulty fwd declaration in shared_ptr_helper.hpp works for us, I am not sure if this is correct:  
  
```  
#ifndef BOOST_NO_MEMBER_TEMPLATE_FRIENDS  
template<class Archive, class T>  
void load(  
    Archive & ar,  
    mwboost::shared_ptr< T > &t,  
    const unsigned int file_version  
);  
  
template<class Archive, class T>  
void load(  
    Archive & ar,  
    std::shared_ptr< T > &t,  
    const unsigned int file_version  
);  
  
template<class Archive, class T>  
void load(  
    Archive & ar,  
    mwboost_132::shared_ptr< T > &t,  
    const unsigned int file_version  
);  
#endif  
```

---

## Comment 8

> Username: ProfFan  
> Created at: 2024-09-23 22:59:24 UTC  
> Url: https://github.com/boostorg/serialization/issues/319#issuecomment-2369711785  

We found the same problem in our project, which prevents builds in latest Ubuntu 24.04.
