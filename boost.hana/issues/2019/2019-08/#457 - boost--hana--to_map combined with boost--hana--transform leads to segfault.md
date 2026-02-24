# #457 - boost::hana::to_map combined with boost::hana::transform leads to segfault [Closed]

> Username: nicktrandafil  
> Created at: 2019-08-07 12:41:18 UTC  
> Updated at: 2022-10-24 18:08:18 UTC  
> Closed at: 2022-10-21 02:36:21 UTC  
> Url: https://github.com/boostorg/hana/issues/457  

Having a simple program with the following source code,  
  
f1.cpp:  
```cpp  
#include "to_map.hpp"  
  
using namespace boost::hana::literals;  
  
void f1() {  
    auto tmp = boost::hana::make_tuple(  
        boost::hana::make_tuple("x1"_s, 1),  
        boost::hana::make_tuple("x2"_s, 2)  
    );  
  
    (void)toMap(tmp)["x1"_s];  
}  
  
```  
  
f2.cpp  
```cpp  
#include "to_map.hpp"  
  
using namespace boost::hana::literals;  
  
void f2() {  
    auto tmp = boost::hana::make_tuple(  
        boost::hana::make_tuple("x1"_s, 1)  
    );  
  
    (void)toMap(tmp)["x1"_s];  
}  
```  
  
to_map.hpp  
```cpp  
#pragma once  
  
#include <boost/hana.hpp>  
  
template <class M>  
auto toMap(M&& m) {  
    return boost::hana::to_map(boost::hana::transform(  
        std::forward<M>(m),  
        [](auto const& x) {  
            return boost::hana::make_pair(boost::hana::at_c<0>(x), boost::hana::at_c<1>(x));  
        }));  
}  
```  
  
main.cpp:  
```cpp  
void f1();  
void f2();  
  
int main() {  
    f1();  
    f2();  
}  
```  
  
and compiling it with *gcc 7.4.0* using the following `CMakeLists.txt` file:  
```  
cmake_minimum_required(VERSION 2.8)  
project(proj)  
find_package(Boost 1.65 QUIET REQUIRED)  
set(CMAKE_CXX_FLAGS "-std=c++17 -g -O0 -fsanitize=address -DBOOST_HANA_CONFIG_ENABLE_STRING_UDL")  
add_executable(${PROJECT_NAME} main.cpp f1.cpp f2.cpp)  
```  
  
and running the program causes segmentation fault. **Putting the code into a single `main.cpp` file makes the crash to disappear**.  
  
There is no segfault using *Clang*.  
  
Output of the `-fsantitize=address` tool:  
```  
user@user-B360M-HD3:~/workspace/build-proj-Desktop-Debug$ ./proj  
=================================================================  
==1899==ERROR: AddressSanitizer: global-buffer-overflow on address 0x55b19cc1f1c0 at pc 0x55b19cc144f4 bp 0x7ffe3cbe8230 sp 0x7ffe3cbe8220  
WRITE of size 4 at 0x55b19cc1f1c0 thread T0  
    #0 0x55b19cc144f3 in decltype(auto) boost::hana::insert_t::operator()<boost::hana::detail::map_impl<boost::hana::detail::hash_table<>, boost::hana::basic_tuple<> >, boost::hana::pair<boost::hana::string<(char)120, (char)49>, int> >(boost::hana::detail::map_impl<boost::hana::detail::hash_table<>, boost::hana::basic_tuple<> >&&, boost::hana::pair<boost::hana::string<(char)120, (char)49>, int>&&) const (/home/user/workspace/build-proj-Desktop-Debug/proj+0x194f3)  
    #1 0x55b19cc15df3 in decltype(auto) boost::hana::detail::variadic::foldl1_impl<2u, boost::hana::when<true> >::apply<boost::hana::insert_t const&, boost::hana::detail::map_impl<boost::hana::detail::hash_table<>, boost::hana::basic_tuple<> >, boost::hana::pair<boost::hana::string<(char)120, (char)49>, int> >(boost::hana::insert_t const&, boost::hana::detail::map_impl<boost::hana::detail::hash_table<>, boost::hana::basic_tuple<> >&&, boost::hana::pair<boost::hana::string<(char)120, (char)49>, int>&&) /usr/include/boost/hana/detail/variadic/foldl1.hpp:34  
    #2 0x55b19cc15e24 in decltype(auto) boost::hana::detail::variadic::foldl1_t::operator()<boost::hana::insert_t const&, boost::hana::detail::map_impl<boost::hana::detail::hash_table<>, boost::hana::basic_tuple<> >, boost::hana::pair<boost::hana::string<(char)120, (char)49>, int> >(boost::hana::insert_t const&, boost::hana::detail::map_impl<boost::hana::detail::hash_table<>, boost::hana::basic_tuple<> >&&, boost::hana::pair<boost::hana::string<(char)120, (char)49>, int>&&) const /usr/include/boost/hana/detail/variadic/foldl1.hpp:206  
    #3 0x55b19cc15e9f in decltype(auto) boost::hana::detail::variadic_foldl1<boost::hana::insert_t const&, boost::hana::detail::map_impl<boost::hana::detail::hash_table<>, boost::hana::basic_tuple<> > >::operator()<boost::hana::pair<boost::hana::string<(char)120, (char)49>, int> >(boost::hana::pair<boost::hana::string<(char)120, (char)49>, int>&&) const /usr/include/boost/hana/fold_left.hpp:69  
    #4 0x55b19cc15ecf in decltype(auto) boost::hana::unpack_impl<boost::hana::basic_tuple_tag, void>::apply<0ul, boost::hana::pair<boost::hana::string<(char)120, (char)49>, int>, boost::hana::detail::variadic_foldl1<boost::hana::insert_t const&, boost::hana::detail::map_impl<boost::hana::detail::hash_table<>, boost::hana::basic_tuple<> > > >(boost::hana::detail::basic_tuple_impl<std::integer_sequence<unsigned long, 0ul>, boost::hana::pair<boost::hana::string<(char)120, (char)49>, int> >&&, boost::hana::detail::variadic_foldl1<boost::hana::insert_t const&, boost::hana::detail::map_impl<boost::hana::detail::hash_table<>, boost::hana::basic_tuple<> > >&&) /usr/include/boost/hana/basic_tuple.hpp:131  
    #5 0x55b19cc15ef8 in decltype(auto) boost::hana::unpack_t::operator()<boost::hana::basic_tuple<boost::hana::pair<boost::hana::string<(char)120, (char)49>, int> >, boost::hana::detail::variadic_foldl1<boost::hana::insert_t const&, boost::hana::detail::map_impl<boost::hana::detail::hash_table<>, boost::hana::basic_tuple<> > > >(boost::hana::basic_tuple<boost::hana::pair<boost::hana::string<(char)120, (char)49>, int> >&&, boost::hana::detail::variadic_foldl1<boost::hana::insert_t const&, boost::hana::detail::map_impl<boost::hana::detail::hash_table<>, boost::hana::basic_tuple<> > >&&) const /usr/include/boost/hana/unpack.hpp:47  
    #6 0x55b19cc15f21 in decltype(auto) boost::hana::unpack_impl<boost::hana::tuple_tag, void>::apply<boost::hana::tuple<boost::hana::pair<boost::hana::string<(char)120, (char)49>, int> >, boost::hana::detail::variadic_foldl1<boost::hana::insert_t const&, boost::hana::detail::map_impl<boost::hana::detail::hash_table<>, boost::hana::basic_tuple<> > > >(boost::hana::tuple<boost::hana::pair<boost::hana::string<(char)120, (char)49>, int> >&&, boost::hana::detail::variadic_foldl1<boost::hana::insert_t const&, boost::hana::detail::map_impl<boost::hana::detail::hash_table<>, boost::hana::basic_tuple<> > >&&) /usr/include/boost/hana/tuple.hpp:225  
    #7 0x55b19cc15f4a in decltype(auto) boost::hana::unpack_t::operator()<boost::hana::tuple<boost::hana::pair<boost::hana::string<(char)120, (char)49>, int> >, boost::hana::detail::variadic_foldl1<boost::hana::insert_t const&, boost::hana::detail::map_impl<boost::hana::detail::hash_table<>, boost::hana::basic_tuple<> > > >(boost::hana::tuple<boost::hana::pair<boost::hana::string<(char)120, (char)49>, int> >&&, boost::hana::detail::variadic_foldl1<boost::hana::insert_t const&, boost::hana::detail::map_impl<boost::hana::detail::hash_table<>, boost::hana::basic_tuple<> > >&&) const /usr/include/boost/hana/unpack.hpp:47  
    #8 0x55b19cc16058 in decltype(auto) boost::hana::fold_left_impl<boost::hana::tuple_tag, boost::hana::when<true> >::apply<boost::hana::tuple<boost::hana::pair<boost::hana::string<(char)120, (char)49>, int> >, boost::hana::detail::map_impl<boost::hana::detail::hash_table<>, boost::hana::basic_tuple<> >, boost::hana::insert_t const&>(boost::hana::tuple<boost::hana::pair<boost::hana::string<(char)120, (char)49>, int> >&&, boost::hana::detail::map_impl<boost::hana::detail::hash_table<>, boost::hana::basic_tuple<> >&&, boost::hana::insert_t const&) /usr/include/boost/hana/fold_left.hpp:81  
    #9 0x55b19cc160d6 in decltype(auto) boost::hana::fold_left_t::operator()<boost::hana::tuple<boost::hana::pair<boost::hana::string<(char)120, (char)49>, int> >, boost::hana::detail::map_impl<boost::hana::detail::hash_table<>, boost::hana::basic_tuple<> >, boost::hana::insert_t const&>(boost::hana::tuple<boost::hana::pair<boost::hana::string<(char)120, (char)49>, int> >&&, boost::hana::detail::map_impl<boost::hana::detail::hash_table<>, boost::hana::basic_tuple<> >&&, boost::hana::insert_t const&) const /usr/include/boost/hana/fold_left.hpp:39  
    #10 0x55b19cc1618f in decltype(auto) boost::hana::to_impl<boost::hana::map_tag, boost::hana::tuple_tag, boost::hana::when<true> >::apply<boost::hana::tuple<boost::hana::pair<boost::hana::string<(char)120, (char)49>, int> > >(boost::hana::tuple<boost::hana::pair<boost::hana::string<(char)120, (char)49>, int> >&&) /usr/include/boost/hana/map.hpp:572  
    #11 0x55b19cc161f9 in decltype(auto) boost::hana::to_t<boost::hana::map_tag>::operator()<boost::hana::tuple<boost::hana::pair<boost::hana::string<(char)120, (char)49>, int> > >(boost::hana::tuple<boost::hana::pair<boost::hana::string<(char)120, (char)49>, int> >&&) const /usr/include/boost/hana/core/to.hpp:76  
    #12 0x55b19cc16311 in auto toMap<boost::hana::tuple<boost::hana::tuple<boost::hana::string<(char)120, (char)49>, int> >&>(boost::hana::tuple<boost::hana::tuple<boost::hana::string<(char)120, (char)49>, int> >&) /home/user/workspace/proj/to_map.hpp:11  
    #13 0x55b19cc1591c in f2() /home/user/workspace/proj/f2.cpp:10  
    #14 0x55b19cc13297 in main /home/user/workspace/proj/main.cpp:6  
    #15 0x7f88fee46b96 in __libc_start_main (/lib/x86_64-linux-gnu/libc.so.6+0x21b96)  
    #16 0x55b19cc131a9 in _start (/home/user/workspace/build-proj-Desktop-Debug/proj+0x181a9)  
  
0x55b19cc1f1c1 is located 0 bytes to the right of global variable 'insert' defined in '/usr/include/boost/hana/fwd/insert.hpp:29:24' (0x55b19cc1f1c0) of size 1  
  'insert' is ascii string ''  
SUMMARY: AddressSanitizer: global-buffer-overflow (/home/user/workspace/build-proj-Desktop-Debug/proj+0x194f3) in decltype(auto) boost::hana::insert_t::operator()<boost::hana::detail::map_impl<boost::hana::detail::hash_table<>, boost::hana::basic_tuple<> >, boost::hana::pair<boost::hana::string<(char)120, (char)49>, int> >(boost::hana::detail::map_impl<boost::hana::detail::hash_table<>, boost::hana::basic_tuple<> >&&, boost::hana::pair<boost::hana::string<(char)120, (char)49>, int>&&) const  
Shadow bytes around the buggy address:  
  0x0ab6b397bde0: 01 f9 f9 f9 f9 f9 f9 f9 01 f9 f9 f9 f9 f9 f9 f9  
  0x0ab6b397bdf0: 01 f9 f9 f9 f9 f9 f9 f9 01 f9 f9 f9 f9 f9 f9 f9  
  0x0ab6b397be00: 01 f9 f9 f9 f9 f9 f9 f9 01 f9 f9 f9 f9 f9 f9 f9  
  0x0ab6b397be10: 01 f9 f9 f9 f9 f9 f9 f9 01 f9 f9 f9 f9 f9 f9 f9  
  0x0ab6b397be20: 01 f9 f9 f9 f9 f9 f9 f9 01 f9 f9 f9 f9 f9 f9 f9  
=>0x0ab6b397be30: 01 f9 f9 f9 f9 f9 f9 f9[01]f9 f9 f9 f9 f9 f9 f9  
  0x0ab6b397be40: 01 f9 f9 f9 f9 f9 f9 f9 01 f9 f9 f9 f9 f9 f9 f9  
  0x0ab6b397be50: 01 f9 f9 f9 f9 f9 f9 f9 01 f9 f9 f9 f9 f9 f9 f9  
  0x0ab6b397be60: 01 f9 f9 f9 f9 f9 f9 f9 01 f9 f9 f9 f9 f9 f9 f9  
  0x0ab6b397be70: 01 f9 f9 f9 f9 f9 f9 f9 01 f9 f9 f9 f9 f9 f9 f9  
  0x0ab6b397be80: 01 f9 f9 f9 f9 f9 f9 f9 01 f9 f9 f9 f9 f9 f9 f9  
Shadow byte legend (one shadow byte represents 8 application bytes):  
  Addressable:           00  
  Partially addressable: 01 02 03 04 05 06 07  
  Heap left redzone:       fa  
  Freed heap region:       fd  
  Stack left redzone:      f1  
  Stack mid redzone:       f2  
  Stack right redzone:     f3  
  Stack after return:      f5  
  Stack use after scope:   f8  
  Global redzone:          f9  
  Global init order:       f6  
  Poisoned by user:        f7  
  Container overflow:      fc  
  Array cookie:            ac  
  Intra object redzone:    bb  
  ASan internal:           fe  
  Left alloca redzone:     ca  
  Right alloca redzone:    cb  
==1899==ABORTING  
```  
  
The program is pretty strighforward. There is no usage of global variables except hana functional objects. It seems to me that the client code does nothing wrong, so I assume there is a bug in Hana.  
  
Attached the source code [proj.tar.gz](https://github.com/boostorg/hana/files/3476984/proj.tar.gz)

---

## Comment 1

> Username: ldionne  
> Created at: 2020-09-03 16:03:29 UTC  
> Updated at: 2020-09-03 16:05:10 UTC  
> Url: https://github.com/boostorg/hana/issues/457#issuecomment-686592988  

Live reproducers:  
- GCC 10: https://wandbox.org/permlink/6MKIrjuyHOalFe6G  
- Clang trunk: https://wandbox.org/permlink/AC82HwlMsNSIj3yh

---

## Comment 2

> Username: ldionne  
> Created at: 2020-09-03 16:28:42 UTC  
> Url: https://github.com/boostorg/hana/issues/457#issuecomment-686608461  

This is weird, actually I get an Asan error with just (https://wandbox.org/permlink/60Op7hA2vKj2L6ID):  
  
```  
#include <boost/hana.hpp>  
  
int main() { }  
```  
  
Looks like it has something to do with how we're declaring global function objects, but it indeed doesn't look to me like it's an actual issue specific to `transform`/`to_map`.

---

## Comment 3

> Username: ricejasonf  
> Created at: 2020-09-03 16:43:15 UTC  
> Updated at: 2020-09-03 16:43:59 UTC  
> Url: https://github.com/boostorg/hana/issues/457#issuecomment-686616251  

Just `int main() { }` reproduces that. :scream_cat:   
  
I was thinking it was that same issue with `curry_or_call`.

---

## Comment 4

> Username: ldionne  
> Created at: 2020-09-03 16:57:31 UTC  
> Updated at: 2020-09-03 17:00:58 UTC  
> Url: https://github.com/boostorg/hana/issues/457#issuecomment-686624263  

Looking back, we clearly have ODR violations due to global `constexpr` objects. We could use `inline` variables to fix that. I don't know for sure whether it'll help this issue, though. I created #474 to look at that issue.

---

## Comment 5

> Username: ldionne  
> Created at: 2022-10-21 02:36:21 UTC  
> Url: https://github.com/boostorg/hana/issues/457#issuecomment-1286381057  

I just tried reproducing locally with ASAN and I can't anymore. Please reopen if you're still seeing this. We did fix the ODR violation issues that were potentially related.

---

## Comment 6

> Username: nicktrandafil  
> Created at: 2022-10-24 18:08:18 UTC  
> Url: https://github.com/boostorg/hana/issues/457#issuecomment-1289402544  

The attached project still terminates with segmentation fault and ASAN error with Boost 1.80.0-2 and GCC 12.2.0. I noticed that with optimization level `-O2` the program works fine but not with `-O0`.
