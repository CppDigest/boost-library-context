# #168 - tuple_size does not work on clang [Open]

> Username: egorpugin  
> Created at: 2024-04-13 17:50:34 UTC  
> Updated at: 2025-06-20 14:17:23 UTC  
> Url: https://github.com/boostorg/pfr/issues/168  

Works both for gcc and msvc, but not clang.  
  
https://godbolt.org/z/bcochYz5P  
  
```#include <string>  
#include <variant>  
#include <optional>  
#include <vector>  
#include <memory>  
#include <boost/pfr.hpp>  
  
using Boolean = bool;  
using Integer = std::int64_t;  
using Float = double;  
using String = std::string;  
  
template <typename T>  
using Optional = std::optional<T>;  
template <typename ... Args>  
using Ptr = std::unique_ptr<Args...>;  
template <typename ... Args>  
using Variant = std::variant<Args...>;  
template <typename ... Args>  
using Vector = std::vector<Args...>;  
  
// This object represents one button of the reply keyboard. For simple text buttons, String can be used instead of this object to specify the button text. The optional fields web_app, request_users, request_chat, request_contact, request_location, and request_poll are mutually exclusive.  
struct KeyboardButton  
{  
    // Text of the button. If none of the optional fields are used, it will be sent as a message when the button is pressed  
    String text;  
    // Optional. If True, the user's phone number will be sent as a contact when the button is pressed. Available in private chats only.  
    Optional<Boolean> request_contact;  
    // Optional. If True, the user's current location will be sent when the button is pressed. Available in private chats only.  
    Optional<Boolean> request_location;  
};  
  
// This object represents a custom keyboard with reply options (see Introduction to bots for details and examples).  
struct ReplyKeyboardMarkup  
{  
    // Array of button rows, each represented by an Array of KeyboardButton objects  
    Vector<Vector<Ptr<KeyboardButton>>> keyboard;  
};  
  
struct sendAnimationRequest  
{  
    // Additional interface options. A JSON-serialized object for an inline keyboard, custom reply keyboard, instructions to remove a reply keyboard or to force a reply from the user. Not supported for messages sent on behalf of a business account  
    Optional<Variant<ReplyKeyboardMarkup>> reply_markup;  
};  
  
int main() {  
    return boost::pfr::tuple_size<sendAnimationRequest>::value;  
}  
```  
  
```  
In file included from <source>:1:  
In file included from /opt/compiler-explorer/gcc-13.2.0/lib/gcc/x86_64-linux-gnu/13.2.0/../../../../include/c++/13.2.0/string:42:  
In file included from /opt/compiler-explorer/gcc-13.2.0/lib/gcc/x86_64-linux-gnu/13.2.0/../../../../include/c++/13.2.0/bits/char_traits.h:57:  
/opt/compiler-explorer/gcc-13.2.0/lib/gcc/x86_64-linux-gnu/13.2.0/../../../../include/c++/13.2.0/bits/stl_construct.h:115:4: error: no matching function for call to 'construct_at'  
  115 |           std::construct_at(__p, std::forward<_Args>(__args)...);  
      |           ^~~~~~~~~~~~~~~~~  
/opt/compiler-explorer/gcc-13.2.0/lib/gcc/x86_64-linux-gnu/13.2.0/../../../../include/c++/13.2.0/bits/stl_uninitialized.h:120:11: note: in instantiation of function template specialization 'std::_Construct<std::unique_ptr<KeyboardButton>, const std::unique_ptr<KeyboardButton> &>' requested here  
  120 |             std::_Construct(std::__addressof(*__cur), *__first);  
      |                  ^  
/opt/compiler-explorer/gcc-13.2.0/lib/gcc/x86_64-linux-gnu/13.2.0/../../../../include/c++/13.2.0/bits/stl_uninitialized.h:371:14: note: in instantiation of function template specialization 'std::__do_uninit_copy<__gnu_cxx::__normal_iterator<const std::unique_ptr<KeyboardButton> *, std::vector<std::unique_ptr<KeyboardButton>>>, std::unique_ptr<KeyboardButton> *>' requested here  
  371 |         return std::__do_uninit_copy(__first, __last, __result);  
      |                     ^  
/opt/compiler-explorer/gcc-13.2.0/lib/gcc/x86_64-linux-gnu/13.2.0/../../../../include/c++/13.2.0/bits/stl_vector.h:603:9: note: in instantiation of function template specialization 'std::__uninitialized_copy_a<__gnu_cxx::__normal_iterator<const std::unique_ptr<KeyboardButton> *, std::vector<std::unique_ptr<KeyboardButton>>>, std::unique_ptr<KeyboardButton> *, std::unique_ptr<KeyboardButton>>' requested here  
  603 |           std::__uninitialized_copy_a(__x.begin(), __x.end(),  
      |                ^  
/opt/compiler-explorer/gcc-13.2.0/lib/gcc/x86_64-linux-gnu/13.2.0/../../../../include/c++/13.2.0/bits/stl_construct.h:97:39: note: in instantiation of member function 'std::vector<std::unique_ptr<KeyboardButton>>::vector' requested here  
   97 |     { return ::new((void*)__location) _Tp(std::forward<_Args>(__args)...); }  
      |                                       ^  
/opt/compiler-explorer/gcc-13.2.0/lib/gcc/x86_64-linux-gnu/13.2.0/../../../../include/c++/13.2.0/bits/stl_construct.h:115:9: note: in instantiation of function template specialization 'std::construct_at<std::vector<std::unique_ptr<KeyboardButton>>, const std::vector<std::unique_ptr<KeyboardButton>> &>' requested here  
  115 |           std::construct_at(__p, std::forward<_Args>(__args)...);  
      |                ^  
/opt/compiler-explorer/gcc-13.2.0/lib/gcc/x86_64-linux-gnu/13.2.0/../../../../include/c++/13.2.0/bits/stl_uninitialized.h:120:11: note: (skipping 23 contexts in backtrace; use -ftemplate-backtrace-limit=0 to see all)  
  120 |             std::_Construct(std::__addressof(*__cur), *__first);  
      |                  ^  
/opt/compiler-explorer/libs/boost_1_84_0/boost/pfr/detail/fields_count.hpp:183:11: note: in instantiation of default argument for 'enable_if_constructible_helper_t<sendAnimationRequest, 161UL>' required here  
  183 | constexpr auto detect_fields_count(detail::multi_element_range, long) noexcept  
      |           ^~~~  
/opt/compiler-explorer/libs/boost_1_84_0/boost/pfr/detail/fields_count.hpp:246:12: note: while substituting explicitly-specified template arguments into function template 'detect_fields_count'   
  246 |     return detail::detect_fields_count<T, 0, middle>(detail::multi_element_range{}, 1L);  
      |            ^  
/opt/compiler-explorer/libs/boost_1_84_0/boost/pfr/detail/fields_count.hpp:305:44: note: in instantiation of function template specialization 'boost::pfr::detail::detect_fields_count_dispatch<sendAnimationRequest, 320UL>' requested here  
  305 |     constexpr std::size_t result = detail::detect_fields_count_dispatch<type>(size_t_<max_fields_count>{}, 1L, 1L);  
      |                                            ^  
/opt/compiler-explorer/libs/boost_1_84_0/boost/pfr/tuple_size.hpp:33:57: note: in instantiation of function template specialization 'boost::pfr::detail::fields_count<sendAnimationRequest>' requested here  
   33 | using tuple_size = detail::size_t_< boost::pfr::detail::fields_count<T>() >;  
      |                                                         ^  
<source>:47:24: note: in instantiation of template type alias 'tuple_size' requested here  
   47 |     return boost::pfr::tuple_size<sendAnimationRequest>::value;  
      |                        ^  
/opt/compiler-explorer/gcc-13.2.0/lib/gcc/x86_64-linux-gnu/13.2.0/../../../../include/c++/13.2.0/bits/stl_construct.h:94:5: note: candidate template ignored: substitution failure [with _Tp = std::unique_ptr<KeyboardButton>, _Args = <const std::unique_ptr<KeyboardButton> &>]: call to deleted constructor of 'std::unique_ptr<KeyboardButton>'  
   94 |     construct_at(_Tp* __location, _Args&&... __args)  
      |     ^  
   95 |     noexcept(noexcept(::new((void*)0) _Tp(std::declval<_Args>()...)))  
   96 |     -> decltype(::new((void*)0) _Tp(std::declval<_Args>()...))  
      |                                 ~~~  
/opt/compiler-explorer/gcc-13.2.0/lib/gcc/x86_64-linux-gnu/13.2.0/../../../../include/c++/13.2.0/bits/stl_construct.h:119:25: error: call to deleted constructor of 'std::unique_ptr<KeyboardButton>'  
  119 |       ::new((void*)__p) _Tp(std::forward<_Args>(__args)...);  
      |                         ^   ~~~~~~~~~~~~~~~~~~~~~~~~~~~  
/opt/compiler-explorer/gcc-13.2.0/lib/gcc/x86_64-linux-gnu/13.2.0/../../../../include/c++/13.2.0/bits/unique_ptr.h:522:7: note: 'unique_ptr' has been explicitly marked deleted here  
  522 |       unique_ptr(const unique_ptr&) = delete;  
      |       ^  
In file included from <source>:4:  
In file included from /opt/compiler-explorer/gcc-13.2.0/lib/gcc/x86_64-linux-gnu/13.2.0/../../../../include/c++/13.2.0/vector:65:  
/opt/compiler-explorer/gcc-13.2.0/lib/gcc/x86_64-linux-gnu/13.2.0/../../../../include/c++/13.2.0/bits/stl_uninitialized.h:90:21: error: static assertion failed due to requirement 'is_constructible<std::unique_ptr<KeyboardButton, std::default_delete<KeyboardButton>>, const std::unique_ptr<KeyboardButton, std::default_delete<KeyboardButton>> &>::value': result type must be constructible from input type  
   90 |       static_assert(is_constructible<_ValueType, _Tp>::value,  
      |                     ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
/opt/compiler-explorer/gcc-13.2.0/lib/gcc/x86_64-linux-gnu/13.2.0/../../../../include/c++/13.2.0/bits/stl_uninitialized.h:182:4: note: in instantiation of function template specialization 'std::__check_constructible<std::unique_ptr<KeyboardButton>, const std::unique_ptr<KeyboardButton> &>' requested here  
  182 |         = _GLIBCXX_USE_ASSIGN_FOR_INIT(_ValueType2, _From);  
      |           ^  
/opt/compiler-explorer/gcc-13.2.0/lib/gcc/x86_64-linux-gnu/13.2.0/../../../../include/c++/13.2.0/bits/stl_uninitialized.h:101:13: note: expanded from macro '_GLIBCXX_USE_ASSIGN_FOR_INIT'  
  101 |     && std::__check_constructible<T, U>()  
      |             ^  
/opt/compiler-explorer/gcc-13.2.0/lib/gcc/x86_64-linux-gnu/13.2.0/../../../../include/c++/13.2.0/bits/stl_uninitialized.h:373:19: note: in instantiation of function template specialization 'std::uninitialized_copy<__gnu_cxx::__normal_iterator<const std::unique_ptr<KeyboardButton> *, std::vector<std::unique_ptr<KeyboardButton>>>, std::unique_ptr<KeyboardButton> *>' requested here  
  373 |       return std::uninitialized_copy(__first, __last, __result);  
      |                   ^  
3 errors generated.  
Compiler returned: 1  
```

---

## Comment 1

> Username: apolukhin  
> Created at: 2025-06-20 14:17:10 UTC  
> Url: https://github.com/boostorg/pfr/issues/168#issuecomment-2991800821  

Looks like a dup of https://github.com/boostorg/pfr/issues/131
