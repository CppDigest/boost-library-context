# #179 - more classification/traits [Open]

> Username: gpeterhoff  
> Created at: 2023-05-01 02:47:41 UTC  
> Updated at: 2023-05-01 02:47:41 UTC  
> Url: https://github.com/boostorg/type_traits/issues/179  

For more precise classifications it is still useful to provide these traits:  
  
* is_character -> char32/char16/char8/wchar/char  
* is_integer -> signed and unsigned int128..8  
* is_unsigned_integer -> is_integer && is_unsigned  
* is_signed_integer -> is_integer && is_signed  
* is_bool -> only bool  
* is_logic -> is_bool || boost::tribool (specialized for tribool in boost::logic)  
I intend to provide a 4-valued logic (https://en.wikipedia.org/wiki/Many-valued_logic#Belnap_logic_(B4)) and a more modern variant of a 3-valued logic (instead of boost::tribool) (and for completeness "normal 2-valued logic"):  
* is_logic -> is_bool4 || is_bool3 || is_bool2 || is_bool  
* is_numeric -> is_floating_point || is_integer  
* is_function_pointer (like is_member_function_pointer) -> is_pointer && is_function<remove_pointer>  
* dependent_false -> "always false" idiom: template <typename... Types> struct dependent_false : public false_type{};  
* is_quotrem_order -> is std::Xdiv_t implemented as {quot, rem} or {rem, quot} ? https://en.cppreference.com/w/cpp/numeric/math/div  
This is needed at https://en.cppreference.com/w/cpp/language/structured_binding  
auto [a, b] = std::div(x, y)  
-> is a quot or rem or is b quot or rem ?  
(PS: maybe you could submit a proposal to the C++ standard committee to set Xdiv_t to a unique order).  
  
  
exact/specific traits eg  
* is_char32, is_wchar, ...  
* is_uint64, is_int16, ...  
* is_std_longdouble/double/float  
* is_std_float128/64/32/16, is_std_bfloat16  
* is_boost_float128/80/64/32/16  
* is_float64 -> is_std_float64 || is_boost_float64, ...  
  
thx  
Gero
