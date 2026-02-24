# #141 - small_vector does not propagate no throw properties of move operation of contained type [Closed]

> Username: adesitter  
> Created at: 2020-01-30 17:14:34 UTC  
> Updated at: 2020-12-29 23:21:18 UTC  
> Closed at: 2020-12-29 23:19:32 UTC  
> Url: https://github.com/boostorg/container/issues/141  

Considering  
#include <boost/container/small_vector.hpp>  
#include <type_traits>  
  
struct S_trivial {  
  int i;  
};  
static_assert(std::is_nothrow_move_constructible<S_trivial>::value, "");  
static_assert(std::is_nothrow_move_assignable<S_trivial>::value, "");  
  
struct S1 {  
  int i = 0;  
};  
static_assert(std::is_nothrow_move_constructible<S1>::value, "");  
static_assert(std::is_nothrow_move_assignable<S1>::value, "");  
  
struct S2 {  
  int i = 0;  
  S2(S2&&) noexcept;  
  S2& operator=(S2&&) noexcept;  
};  
static_assert(std::is_nothrow_move_constructible<S2>::value, "");  
static_assert(std::is_nothrow_move_assignable<S2>::value, "");  
  
// Succeed  
static_assert(std::is_nothrow_move_constructible<boost::container::small_vector<S_trivial, 1>>::value, "");  
static_assert(std::is_nothrow_move_assignable<boost::container::small_vector<S_trivial, 1>>::value, "");  
  
// Fail  
static_assert(std::is_nothrow_move_constructible<boost::container::small_vector<S1, 1>>::value, "");  
static_assert(std::is_nothrow_move_assignable<boost::container::small_vector<S1, 1>>::value, "");  
  
// Fail  
static_assert(std::is_nothrow_move_constructible<boost::container::small_vector<S2, 1>>::value, "");  
static_assert(std::is_nothrow_move_assignable<boost::container::small_vector<S2, 1>>::value, "");  
  
and using g++ 9.2 and boost 1.71, one can see that for S1 and S2 the noexcept propertties of the move operations are not propagated.

---

## Comment 1

> Username: adesitter  
> Created at: 2020-01-31 11:33:56 UTC  
> Url: https://github.com/boostorg/container/issues/141#issuecomment-580698579  

It turns out that you can make it work with:  
  
#include <type_traits>  
#define BOOST_MOVE_HAS_NOTHROW_MOVE_ASSIGN(T) std::is_nothrow_move_assignable<T>::value  
#include <boost/container/small_vector.hpp>

---

## Comment 2

> Username: adesitter  
> Created at: 2020-06-14 19:59:23 UTC  
> Url: https://github.com/boostorg/container/issues/141#issuecomment-643814784  

This is the same issue as https://svn.boost.org/trac10/ticket/13487

---

## Comment 3

> Username: palebedev  
> Created at: 2020-12-29 11:58:05 UTC  
> Url: https://github.com/boostorg/container/issues/141#issuecomment-752050532  

And likely related to more recent changes described in https://github.com/boostorg/move/issues/35 .

---

## Comment 4

> Username: igaztanaga  
> Created at: 2020-12-29 23:21:18 UTC  
> Url: https://github.com/boostorg/container/issues/141#issuecomment-752273673  

Many thanks for the report and the patience. Apart from the boostorg/move#35, small_vector's move constructor incorrectly depended on is_nothrow_move_assignable.
