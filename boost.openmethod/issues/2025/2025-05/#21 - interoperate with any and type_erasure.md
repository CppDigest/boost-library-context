# #21 - interoperate with any and type_erasure [Open]

> Username: jll63  
> Created at: 2025-05-12 21:25:03 UTC  
> Updated at: 2025-05-12 21:25:09 UTC  
> Url: https://github.com/boostorg/openmethod/issues/21  

Steven Watanabe's experiments:  
  
```c++  
#include <boost/openmethod.hpp>  
#include <boost/openmethod/compiler.hpp>  
#include <boost/openmethod/policies.hpp>  
  
#include <any>  
#include <boost/mp11/list.hpp>  
#include <concepts>  
#include <iostream>  
  
using namespace boost::openmethod;  
using namespace boost::mp11;  
  
struct any_rtti : boost::openmethod::policies::rtti {  
  template <class Class>  
  static constexpr auto is_polymorphic = std::is_same_v<Class, std::any>;  
  
  template <typename T> static type_id static_type() {  
    return reinterpret_cast<type_id>(&typeid(T));  
  }  
  
  template <std::same_as<std::any> T>  
  static type_id dynamic_type(const T &obj) {  
    return reinterpret_cast<type_id>(&obj.type());  
  }  
  
  template <class Stream> static void type_name(type_id type, Stream &stream) {  
    stream << reinterpret_cast<const std::type_info *>(type)->name();  
  }  
  
  static std::type_index type_index(type_id type) {  
    return std::type_index(*reinterpret_cast<const std::type_info *>(type));  
  }  
  
  template <typename D, typename B> static D dynamic_cast_ref(B &obj) {  
    return std::any_cast<D>(obj);  
  }  
};  
  
struct any_policy  
    : default_policy::fork<any_policy>::replace<policies::rtti, any_rtti> {};  
  
BOOST_OPENMETHOD(foo, (virtual_<const std::any &>), void, any_policy)  
  
// BOOST_OPENMETHOD_CLASSES(int, std::any, any_policy)  
using boost::openmethod::detail::class_declaration_aux;  
class_declaration_aux<any_policy, mp_list<std::any>> x;  
class_declaration_aux<any_policy, mp_list<int, std::any>> y;  
class_declaration_aux<any_policy, mp_list<double, std::any>> z;  
  
BOOST_OPENMETHOD_OVERRIDE(foo, (const int &i), void) {  
  std::cout << i << std::endl;  
}  
  
BOOST_OPENMETHOD_OVERRIDE(foo, (const double &d), void) {  
  std::cout << d << std::endl;  
}  
  
int main() {  
  boost::openmethod::initialize<any_policy>();  
  std::any a(5);  
  std::any d(3.14);  
  foo(a);  
  foo(d);  
}  
```  
  
```c++  
#include <boost/openmethod.hpp>  
#include <boost/openmethod/compiler.hpp>  
#include <boost/openmethod/policies.hpp>  
  
#include <boost/type_erasure/any.hpp>  
#include <boost/type_erasure/any_cast.hpp>  
#include <boost/type_erasure/derived.hpp>  
#include <boost/type_erasure/placeholder.hpp>  
#include <boost/type_erasure/typeid_of.hpp>  
  
#include <boost/mp11/list.hpp>  
  
#include <iostream>  
  
using namespace boost::openmethod;  
  
template <typename T> constexpr bool is_any = false;  
template <typename C, typename T>  
constexpr bool is_any<boost::type_erasure::any<C, T>> = true;  
  
struct type_erased_rtti : policies::rtti {  
  template <class Class> static constexpr auto is_polymorphic = is_any<Class>;  
  
  template <typename T> static type_id static_type() {  
    if constexpr (is_any<T>) {  
      return reinterpret_cast<type_id>(&typeid(void));  
    } else {  
      return reinterpret_cast<type_id>(&typeid(T));  
    }  
  }  
  
  template <typename T> static type_id dynamic_type(const T &obj) {  
    return reinterpret_cast<type_id>(&boost::type_erasure::typeid_of(obj));  
  }  
  
  template <class Stream> static void type_name(type_id type, Stream &stream) {  
    stream << reinterpret_cast<const std::type_info *>(type)->name();  
  }  
  
  static std::type_index type_index(type_id type) {  
    return std::type_index(*reinterpret_cast<const std::type_info *>(type));  
  }  
  
  template <typename D, typename B> static D dynamic_cast_ref(B &&obj) {  
    // cast through void* for a fast, unchecked cast  
    using unref = std::remove_reference_t<D>;  
    if constexpr (std::is_const_v<unref>) {  
      return *static_cast<unref *>(  
          boost::type_erasure::any_cast<const void *>(&obj));  
    } else {  
      return *static_cast<unref *>(boost::type_erasure::any_cast<void *>(&obj));  
    }  
  }  
};  
  
struct type_erased_policy  
    : boost::openmethod::policies::debug::fork<type_erased_policy>::replace<  
          boost::openmethod::policies::rtti, type_erased_rtti> {};  
  
BOOST_OPENMETHOD_CLASSES(void, type_erased_policy)  
  
template <typename Policy, typename T, typename... Bases>  
boost::openmethod::detail::class_declaration_aux<  
    Policy, boost::mp11::mp_list<T, Bases...>>  
    global_register;  
  
template <typename T, typename Policy> struct openmethod_vptr {  
  static boost::openmethod::vptr_type apply(const T &) {  
    const auto &_ = global_register<Policy, T, void>;  
    return Policy::template static_vptr<T>;  
  }  
};  
  
namespace boost::type_erasure {  
template <typename T, typename Policy, typename Base>  
struct concept_interface<::openmethod_vptr<T, Policy>, Base, T> : Base {  
  friend boost::openmethod::vptr_type boost_openmethod_vptr(  
      const typename boost::type_erasure::derived<Base>::type &arg) {  
    return boost::type_erasure::call(::openmethod_vptr<T, Policy>(), arg);  
  }  
};  
} // namespace boost::type_erasure  
  
template <typename T = boost::type_erasure::_self,  
          typename Policy = type_erased_policy>  
using openmethod =  
    boost::mp11::mp_list<boost::type_erasure::typeid_<>,  
                         openmethod_vptr<boost::type_erasure::_self, Policy>>;  
  
using Base = boost::type_erasure::any<boost::mp11::mp_list<  
    openmethod<>, boost::type_erasure::copy_constructible<>>>;  
using BaseRef =  
    boost::type_erasure::any<openmethod<>, boost::type_erasure::_self &>;  
  
BOOST_OPENMETHOD(foo, (virtual_<const BaseRef &>), void, type_erased_policy)  
  
BOOST_OPENMETHOD_OVERRIDE(foo, (int &i), void) { i = 5; }  
  
BOOST_OPENMETHOD_OVERRIDE(foo, (double &d), void) { d = 2.718; }  
  
int main() {  
  boost::openmethod::initialize<type_erased_policy>();  
  int i = 0;  
  double d = 0;  
  BaseRef b1(i);  
  BaseRef b2(d);  
  foo(b1);  
  foo(b2);  
  std::cout << i << std::endl;  
  std::cout << d << std::endl;  
  Base b3(42);  
  foo(b3);  
  std::cout << any_cast<int>(b3) << std::endl;  
}  
```
