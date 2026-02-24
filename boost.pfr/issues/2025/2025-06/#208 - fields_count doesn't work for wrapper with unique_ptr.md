# #208 - fields_count doesn't work for wrapper with unique_ptr [Closed]

> Username: MBkkt  
> Created at: 2025-06-15 09:37:34 UTC  
> Updated at: 2025-09-14 11:59:26 UTC  
> Closed at: 2025-09-14 11:59:26 UTC  
> Url: https://github.com/boostorg/pfr/issues/208  

## Setup  
  
* compiler: clang-19  
* c++ standard: 26  
* libc++: libc++, abi v2, patched (without constexpr dtor for unique_ptr)  
* pfr  
```  
#define BOOST_PFR_USE_STD_MAKE_INTEGRAL_SEQUENCE 1  
#include <boost/pfr.hpp>  
#include <boost/pfr/detail/for_each_field.hpp>  
#include <boost/pfr/tuple_size.hpp>  
  
static_assert(BOOST_PFR_USE_CPP17);  
static_assert(BOOST_PFR_USE_LOOPHOLE == 0);  
static_assert(BOOST_PFR_USE_STD_MAKE_INTEGRAL_SEQUENCE);  
static_assert(BOOST_PFR_HAS_GUARANTEED_COPY_ELISION);  
static_assert(BOOST_PFR_ENABLE_IMPLICIT_REFLECTION);  
static_assert(BOOST_PFR_CORE_NAME_ENABLED);  
#ifndef BOOST_PFR_FUNCTION_SIGNATURE  
static_assert(false);  
#endif  
#ifndef BOOST_PFR_CORE_NAME_PARSING  
static_assert(false);  
#endif  
static_assert(BOOST_PFR_ENABLED);  
```  
  
  
## Test  
  
```cpp  
template<typename T>  
struct Field {  
  using Type = T;  
  
  template<typename... Args>  
    requires(requires { T{std::declval<Args&&>()...}; })  
  constexpr Field(Args&&... args) : _value{std::forward<Args>(args)...} {  
  }  
  
  constexpr auto& operator*(this auto& self) { return self._value; }  
  constexpr auto* operator->(this auto& self) {  
    return std::addressof(self._value);  
  }  
  
  constexpr bool operator==(const Field& rhs) const = default;  
  constexpr auto operator<=>(const Field& rhs) const = default;  
  
 private:  
  [[no_unique_address]] T _value;  
};  
  
struct CustomFields2 {  
  Field<int> some_int;  
  Field<std::vector<int>> some_vector;  
  Field<std::unique_ptr<int>> some_unique_ptr;  
  Field<std::shared_ptr<int>> some_shared_ptr;  
  
  bool operator==(const CustomFields2&) const = default;  
};  
  
TEST(SerializerTest, testCustomFields) {  
  CustomFields2 cf2;  
  boost::pfr::for_each_field_with_name(cf2, [](const auto&, const auto&) {});  
}  
```  
  
## Error   
looks pretty oblivious  
  
```  
In file included from /home/mironov/projects/serenedb/tests/basics/serializer_test.cpp:15:  
In file included from /home/mironov/projects/serenedb/libs/basics/serializer.h:26:  
In file included from /home/mironov/projects/serenedb/third_party/boost/boost/pfr.hpp:13:  
In file included from /home/mironov/projects/serenedb/third_party/boost/boost/pfr/core.hpp:12:  
In file included from /home/mironov/projects/serenedb/third_party/boost/boost/pfr/detail/core.hpp:17:  
In file included from /home/mironov/projects/serenedb/third_party/boost/boost/pfr/detail/core17.hpp:12:  
In file included from /home/mironov/projects/serenedb/third_party/boost/boost/pfr/detail/fields_count.hpp:13:  
/home/mironov/projects/serenedb/third_party/boost/boost/pfr/detail/unsafe_declval.hpp:34:12: error: static_cast from 'typename std::remove_reference<unique_ptr<int>>::type' (aka 'std::unique_ptr<int>') to 'std::unique_ptr<int>' uses deleted function  
   34 |     return static_cast<T>(*ptr);  
      |            ^~~~~~~~~~~~~~~~~~~~  
/home/mironov/projects/serenedb/third_party/boost/boost/pfr/detail/fields_count.hpp:55:24: note: in instantiation of function template specialization 'boost::pfr::detail::unsafe_declval<std::unique_ptr<int>>' requested here  
   55 |         return detail::unsafe_declval<Type>();  
      |                        ^  
/home/mironov/projects/serenedb/tests/basics/serializer_test.cpp:702:44: note: in instantiation of function template specialization 'boost::pfr::detail::ubiq_rref_constructor::operator unique_ptr<std::unique_ptr<int>>' requested here  
  702 |   constexpr Field(Args&&... args) : _value{std::forward<Args>(args)...} {  
      |                                            ^  
/home/mironov/projects/serenedb/build/third_party/llvm/runtimes/include/c++/v1/__memory/unique_ptr.h:138:59: note: candidate constructor (the implicit copy constructor) has been implicitly deleted  
  138 | class _LIBCPP_UNIQUE_PTR_TRIVIAL_ABI _LIBCPP_TEMPLATE_VIS unique_ptr {  
      |                                                           ^  
1 error generated.  
ninja: build stopped: cannot make progress due to previous errors.  
```   
  
## Patch to fix issue  
  
```  
diff --git a/third_party/boost/boost/pfr/detail/fields_count.hpp b/third_party/boost/boost/pfr/detail/fields_count.hpp  
index c3397b3b9..159af79e8 100644  
--- a/third_party/boost/boost/pfr/detail/fields_count.hpp  
+++ b/third_party/boost/boost/pfr/detail/fields_count.hpp  
@@ -52,7 +52,7 @@ struct ubiq_lref_constructor {  
 struct ubiq_rref_constructor {  
     std::size_t ignore;  
     template <class Type> /*constexpr*/ operator Type() const && noexcept {  // Allows initialization of rvalue reference fields and move-only types  
-        return detail::unsafe_declval<Type>();  
+        return detail::unsafe_declval<Type&&>();  
     }  
 };  
   
@@ -144,7 +144,7 @@ struct ubiq_rref_base_asserting {  
     template <class Type> /*constexpr*/ operator Type() const &&  // Allows initialization of rvalue reference fields and move-only types  
         noexcept(detail::static_assert_non_inherited<Derived, Type>())  // force the computation of assert function  
     {  
-        return detail::unsafe_declval<Type>();  
+        return detail::unsafe_declval<Type&&>();  
     }  
 };  
```

---

## Comment 1

> Username: MBkkt  
> Created at: 2025-06-15 09:44:37 UTC  
> Url: https://github.com/boostorg/pfr/issues/208#issuecomment-2973622537  

std::atomic<int> also doesn't work btw

---

## Comment 2

> Username: apolukhin  
> Created at: 2025-09-14 10:24:11 UTC  
> Url: https://github.com/boostorg/pfr/issues/208#issuecomment-3289425439  

Looks like the new unsafe_declval fixes this issue. Will add tests in https://github.com/boostorg/pfr/pull/222
