# #326 - Transform the Type in hana::type [Open]

> Username: ricejasonf  
> Created at: 2017-03-11 00:42:01 UTC  
> Updated at: 2017-09-26 17:37:05 UTC  
> Url: https://github.com/boostorg/hana/issues/326  

Consider a convenience function to perform operations modifying the contained type in a `hana::type` without unwrapping it and wrapping it again. Consider the following example:  
  
```cpp  
// Current method  
decltype(hana::typeid_(hana::back(std::declval<typename some_tuple_type::type>()))){};  
  
// More expressive  
type_transform(some_tuple_type{}, hana::back);  
```  
  
Note: It's important that it strips qualifiers since many functions possibly return references.  
  
Possible Implementation (as discussed in Gitter):  
```cpp  
template <typename T, typename F>  
constexpr auto type_transform(hana::basic_type<T>, F f) {  
  return decltype(hana::typeid_(f(std::declval<T>()))){};  
}  
```

---

## Comment 1

> Username: viboes  
> Created at: 2017-06-09 06:38:11 UTC  
> Url: https://github.com/boostorg/hana/issues/326#issuecomment-307308411  

I don't understand what you are requesting.  
Could you show a *concrete* example of what type_transform will do?

---

## Comment 2

> Username: ricejasonf  
> Created at: 2017-06-09 07:00:17 UTC  
> Url: https://github.com/boostorg/hana/issues/326#issuecomment-307311945  

Like `hana::transform` but for `hana::type`:  
```cpp  
#include <boost/hana.hpp>  
  
namespace hana = boost::hana;  
  
template <typename T, typename F>  
constexpr auto type_transform(hana::basic_type<T>, F f) {  
    return decltype(hana::typeid_(f(std::declval<T>()))){};  
}  
  
int main() {  
  constexpr auto foo = hana::type_c<hana::tuple<int, char, double>>;  
  
  static_assert(type_transform(foo, hana::back) == hana::type_c<double>);  
}  
```

---

## Comment 3

> Username: ricejasonf  
> Created at: 2017-09-26 17:37:05 UTC  
> Url: https://github.com/boostorg/hana/issues/326#issuecomment-332276458  

I'd like to double-down and suggest that `hana::type` be a `Functor`.  
  
This would not be unlike `hana::experimental::types`' implementation as a `Functor`.
