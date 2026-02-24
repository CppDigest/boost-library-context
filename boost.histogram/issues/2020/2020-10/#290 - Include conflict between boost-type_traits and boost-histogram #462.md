# #290 - Include conflict between boost/type_traits and boost/histogram #462 [Closed]

> Username: ralfulrich  
> Created at: 2020-10-12 11:13:56 UTC  
> Updated at: 2020-11-01 19:10:03 UTC  
> Closed at: 2020-11-01 19:10:03 UTC  
> Url: https://github.com/boostorg/histogram/issues/290  

The following minimal program  
```  
#include <boost/type_traits.hpp>  
#include <boost/histogram.hpp>    // reversion include order --> compiles !  
  
int main() {  
  return 0;  
}  
```  
fails to compile with   
```  
In file included from ./boost/histogram/indexed.hpp:12:0,  
                 from ./boost/histogram/algorithm/empty.hpp:11,  
                 from ./boost/histogram/algorithm.hpp:15,  
                 from ./boost/histogram.hpp:27,  
                 from /corsika/test/test.cc:4:  
./boost/histogram/axis/traits.hpp:411:41: error: return type specified for deduction guide  
 constexpr unsigned rank(const Axis& axis) {  
                                         ^  
./boost/histogram/axis/traits.hpp:411:11: error: decl-specifier in declaration of deduction guide  
 constexpr unsigned rank(const Axis& axis) {  
           ^~~~~~~~  
./boost/histogram/axis/traits.hpp:411:1: error: decl-specifier in declaration of deduction guide  
 constexpr unsigned rank(const Axis& axis) {  
 ^~~~~~~~~  
./boost/histogram/axis/traits.hpp:411:41: error: 'signed' or 'unsigned' invalid for '__dguide_rank'  
 constexpr unsigned rank(const Axis& axis) {  
                                         ^  
./boost/histogram/axis/traits.hpp:411:20: error: deduction guide for 'boost::rank<T>' must have trailing return type  
 constexpr unsigned rank(const Axis& axis) {  
                    ^~~~  
In file included from ./boost/type_traits.hpp:145:0,  
                 from /corsika/test/test.cc:2:  
./boost/type_traits/rank.hpp:82:27: note: 'template<class T> struct boost::rank' declared here  
 template <class T> struct rank : public integral_constant<std::size_t, (::boost::detail::rank_imp<T, 0>::value)>{};  
                           ^~~~  
In file included from ./boost/histogram/indexed.hpp:12:0,  
                 from ./boost/histogram/algorithm/empty.hpp:11,  
                 from ./boost/histogram/algorithm.hpp:15,  
                 from ./boost/histogram.hpp:27,  
                 from /corsika/test/test.cc:4:  
./boost/histogram/axis/traits.hpp:411:20: error: deduction guide 'constexpr boost::histogram::axis::traits::rank(const Axis&)-> rank' must not have a function body  
 constexpr unsigned rank(const Axis& axis) {  
                    ^~~~  
./boost/histogram/axis/traits.hpp:421:47: error: return type specified for deduction guide  
 unsigned rank(const axis::variant<Ts...>& axis) {  
                                               ^  
./boost/histogram/axis/traits.hpp:421:1: error: decl-specifier in declaration of deduction guide  
 unsigned rank(const axis::variant<Ts...>& axis) {  
 ^~~~~~~~  
./boost/histogram/axis/traits.hpp:421:47: error: 'signed' or 'unsigned' invalid for '__dguide_rank'  
 unsigned rank(const axis::variant<Ts...>& axis) {  
                                               ^  
./boost/histogram/axis/traits.hpp:421:10: error: deduction guide for 'boost::rank<T>' must have trailing return type  
 unsigned rank(const axis::variant<Ts...>& axis) {  
          ^~~~  
In file included from ./boost/type_traits.hpp:145:0,  
                 from /corsika/test/test.cc:2:  
./boost/type_traits/rank.hpp:82:27: note: 'template<class T> struct boost::rank' declared here  
 template <class T> struct rank : public integral_constant<std::size_t, (::boost::detail::rank_imp<T, 0>::value)>{};  
                           ^~~~  
In file included from ./boost/histogram/indexed.hpp:12:0,  
                 from ./boost/histogram/algorithm/empty.hpp:11,  
                 from ./boost/histogram/algorithm.hpp:15,  
                 from ./boost/histogram.hpp:27,  
                 from /corsika/test/test.cc:4:  
./boost/histogram/axis/traits.hpp:421:10: error: deduction guide 'boost::histogram::axis::traits::rank(const boost::histogram::axis::variant<Ts ...>&)-> rank' must not have a function body  
 unsigned rank(const axis::variant<Ts...>& axis) {  
          ^~~~  
In file included from ./boost/histogram/histogram.hpp:14:0,  
                 from ./boost/histogram/algorithm/project.hpp:15,  
                 from ./boost/histogram/algorithm.hpp:16,  
                 from ./boost/histogram.hpp:27,  
                 from /corsika/test/test.cc:4:  
./boost/histogram/detail/fill.hpp: In function 'typename S::iterator boost::histogram::detail::fill(std::true_type, ArgTraits, std::size_t, S&, A&, const Args&)':  
./boost/histogram/detail/fill.hpp:319:26: error: 'rank' is not a member of 'boost::histogram::axis::traits'  
            axis::traits::rank(axis_get<0>(axes)) == ArgTraits::nargs::value)  
                          ^~~~  
./boost/histogram/detail/fill.hpp:319:26: note: suggested alternatives:  
In file included from /usr/include/c++/7/bits/move.h:54:0,  
                 from /usr/include/c++/7/bits/stl_pair.h:59,  
                 from /usr/include/c++/7/utility:70,  
                 from ./boost/type_traits/detail/has_binary_operator.hpp:41,  
                 from ./boost/type_traits/has_bit_and.hpp:43,  
                 from ./boost/type_traits.hpp:36,  
                 from /corsika/test/test.cc:2:  
/usr/include/c++/7/type_traits:1468:12: note:   'std::rank'  
     struct rank  
            ^~~~  
In file included from ./boost/type_traits.hpp:145:0,  
                 from /corsika/test/test.cc:2:  
./boost/type_traits/rank.hpp:82:27: note:   'boost::rank'  
 template <class T> struct rank : public integral_constant<std::size_t, (::boost::detail::rank_imp<T, 0>::value)>{};  
                           ^~~~  
```  
  
on an ubuntu 18.04 docker container with gcc7.5.0 and boost-headers 1.74  
  
To reproduce, e.g. `sudo docker run -v $PWD:/corsika -it corsika/devel:u-18.04 /bin/bash` and then compile the above mini program, while putting the boost headers right there.

---

## Comment 1

> Username: mreininghaus  
> Created at: 2020-10-12 12:37:22 UTC  
> Url: https://github.com/boostorg/histogram/issues/290#issuecomment-707094092  

The error occurs with `-std=c++17`, however not with `-std=c++14` (both g++ (GCC) 9.2.0).

---

## Comment 2

> Username: HDembinski  
> Created at: 2020-10-30 09:03:19 UTC  
> Updated at: 2020-10-30 10:15:29 UTC  
> Url: https://github.com/boostorg/histogram/issues/290#issuecomment-719432359  

Thank you for reporting this bug. There is a name clash, somewhere `rank` is used without being properly qualified by a namespace.  
  
Link to reproduce:  
https://godbolt.org/z/oK4866  
  
Only gcc seems to be affected and only versions starting with 7.1

---

## Comment 3

> Username: HDembinski  
> Created at: 2020-10-30 13:55:26 UTC  
> Url: https://github.com/boostorg/histogram/issues/290#issuecomment-719566521  

It looks like a bug in gcc. A workaround is to replace line 82 in ``boost/type_traits/rank.hpp`` with  
```c++  
template <class T> using rank = integral_constant<std::size_t, (::boost::detail::rank_imp<T, 0>::value)>;  
```

---

## Comment 4

> Username: HDembinski  
> Created at: 2020-10-30 14:02:26 UTC  
> Updated at: 2020-10-30 14:14:30 UTC  
> Url: https://github.com/boostorg/histogram/issues/290#issuecomment-719570340  

Minimal example to reproduce the problem without including boost at all:  
  
https://godbolt.org/z/hrcYao  
  
msvc is OK, clang is OK, gcc fails

---

## Comment 5

> Username: HDembinski  
> Created at: 2020-10-30 22:31:44 UTC  
> Url: https://github.com/boostorg/histogram/issues/290#issuecomment-719828764  

Simple workarounds for the gcc bug were found by Thomas Ferrand and John Maddock. Those will be applied tomorrow. Next release 1.75 will have the fix for this.

---

## Comment 6

> Username: HDembinski  
> Created at: 2020-11-01 15:09:45 UTC  
> Url: https://github.com/boostorg/histogram/issues/290#issuecomment-720103049  

gcc bug report: https://gcc.gnu.org/bugzilla/show_bug.cgi?id=97663

---

## Comment 7

> Username: HDembinski  
> Created at: 2020-11-01 19:10:03 UTC  
> Url: https://github.com/boostorg/histogram/issues/290#issuecomment-720136751  

fixed in develop
