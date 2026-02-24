# #826 - Can't use {} initialization for creating empty value of type object [Closed]

> Username: matty0ung  
> Created at: 2022-12-26 16:19:32 UTC  
> Updated at: 2022-12-26 19:12:14 UTC  
> Closed at: 2022-12-26 18:33:55 UTC  
> Url: https://github.com/boostorg/json/issues/826  

Using Boost 1.81 on Linux (Ubuntu 22.04) with gcc 11.3.0.  
  
The following compiles OK:  
```  
         boost::json::value myValue(boost::json::object_kind);  
```  
  
However the following (using curly braces instead of round brackets) does not:  
```  
         boost::json::value myValue{boost::json::object_kind};  
```  
  
It would be nice either for braces to work or for the documentation to explicitly warn not to use them.  (Alternatively it would be great if the error message could be less cryptic, but I assume that would be somewhat non-trivial! :smiley: ).  
  
Here's the error output I get, if it's any help:  
```  
In file included from /usr/local/include/boost/json/impl/value_ref.hpp:13,  
                 from /usr/local/include/boost/json/value.hpp:4051,  
                 from /usr/local/include/boost/json/parse.hpp:19,  
                 from ../src/json/BeerJson.cpp:23:  
/usr/local/include/boost/json/value_from.hpp: In instantiation of ‘boost::json::value boost::json::value_from(T&&, boost::json::storage_ptr) [with T = const boost::json::object_kind_t&]’:  
/usr/local/include/boost/json/impl/value_ref.hpp:37:22:   required from ‘static boost::json::value boost::json::value_ref::from_const(const void*, boost::json::storage_ptr) [with T = boost::json::object_kind_t]’  
/usr/local/include/boost/json/value_ref.hpp:204:11:   required from ‘boost::json::value_ref::value_ref(const T&, typename std::enable_if<((! std::is_constructible<boost::core::basic_string_view<char>, T>::value) && (! std::is_same<bool, _U1>::value))>::type*) [with T = boost::json::object_kind_t; typename std::enable_if<((! std::is_constructible<boost::core::basic_string_view<char>, T>::value) && (! std::is_same<bool, _U1>::value))>::type = void]’  
../src/json/BeerJson.cpp:796:60:   required from here  
/usr/local/include/boost/json/value_from.hpp:87:35: error: no matching function for call to ‘value_from_impl(const boost::json::object_kind_t&, std::remove_reference<boost::json::storage_ptr&>::type)’  
   87 |     return detail::value_from_impl(  
      |            ~~~~~~~~~~~~~~~~~~~~~~~^  
   88 |         std::forward<T>(t), std::move(sp));  
      |         ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
In file included from /usr/local/include/boost/json/value_from.hpp:16,  
                 from /usr/local/include/boost/json/impl/value_ref.hpp:13,  
                 from /usr/local/include/boost/json/value.hpp:4051,  
                 from /usr/local/include/boost/json/parse.hpp:19,  
                 from ../src/json/BeerJson.cpp:23:  
/usr/local/include/boost/json/detail/value_from.hpp:183:1: note: candidate: ‘template<class T, class> boost::json::value boost::json::detail::value_from_impl(T&&, boost::json::storage_ptr)’  
  183 | value_from_impl(  
      | ^~~~~~~~~~~~~~~  
/usr/local/include/boost/json/detail/value_from.hpp:183:1: note:   template argument deduction/substitution failed:  
/usr/local/include/boost/json/detail/value_from.hpp:180:39: error: no matching function for call to ‘value_from_helper(boost::json::value&, const boost::json::object_kind_t&, boost::json::detail::priority_tag<5>)’  
  180 |     decltype(detail::value_from_helper(std::declval<value&>(),  
      |              ~~~~~~~~~~~~~~~~~~~~~~~~~^~~~~~~~~~~~~~~~~~~~~~~~  
  181 |         std::declval<T&&>(), priority_tag<5>()))>>  
      |         ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
/usr/local/include/boost/json/detail/value_from.hpp:60:1: note: candidate: ‘template<class T, boost::json::detail::void_t<decltype (tag_invoke(boost::json::value_from_tag(), declval<boost::json::value&>(), declval<T&&>()))>* <anonymous> > void boost::json::detail::value_from_helper(boost::json::value&, T&&, boost::json::detail::priority_tag<5>)’  
   60 | value_from_helper(  
      | ^~~~~~~~~~~~~~~~~  
/usr/local/include/boost/json/detail/value_from.hpp:60:1: note:   template argument deduction/substitution failed:  
/usr/local/include/boost/json/detail/value_from.hpp:57:45: error: ‘tag_invoke’ was not declared in this scope; did you mean ‘boost::json::detail::tag_invoke’?  
   57 | template<class T, void_t<decltype(tag_invoke(value_from_tag(),  
      |                                   ~~~~~~~~~~^~~~~~~~~~~~~~~~~~  
   58 |     std::declval<value&>(), std::declval<T&&>()))>* = nullptr>  
      |     ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
In file included from /usr/local/include/boost/json/value_to.hpp:16,  
                 from /usr/local/include/boost/json.hpp:40,  
                 from ../third-party/valijson/include/valijson/adapters/boost_json_adapter.hpp:30,  
                 from ../src/json/BeerJson.cpp:26:  
/usr/local/include/boost/json/detail/value_to.hpp:254:1: note: ‘boost::json::detail::tag_invoke’ declared here  
  254 | tag_invoke(  
      | ^~~~~~~~~~  
In file included from /usr/local/include/boost/json/value_from.hpp:16,  
                 from /usr/local/include/boost/json/impl/value_ref.hpp:13,  
                 from /usr/local/include/boost/json/value.hpp:4051,  
                 from /usr/local/include/boost/json/parse.hpp:19,  
                 from ../src/json/BeerJson.cpp:23:  
/usr/local/include/boost/json/detail/value_from.hpp:75:1: note: candidate: ‘template<class T, typename std::enable_if<std::integral_constant<bool, ((((((((std::is_same<typename std::remove_cv<typename std::remove_reference<_Tp>::type>::type, boost::json::value>::value || std::is_same<typename std::remove_cv<typename std::remove_reference<_Tp>::type>::type, boost::json::object>::value) || std::is_same<typename std::remove_cv<typename std::remove_reference<_Tp>::type>::type, boost::json::array>::value) || std::is_same<typename std::remove_cv<typename std::remove_reference<_Tp>::type>::type, boost::json::string>::value) || std::is_same<typename std::remove_cv<typename std::remove_reference<_Tp>::type>::type, boost::core::basic_string_view<char> >::value) || std::is_arithmetic<typename std::remove_cv<typename std::remove_reference<_Tp>::type>::type>::value) || std::is_same<typename std::remove_cv<typename std::remove_reference<_Tp>::type>::type, const char*>::value) || std::is_same<typename std::remove_cv<typename std::remove_reference<_Tp>::type>::type, std::initializer_list<boost::json::value_ref> >::value) || std::is_same<typename std::remove_cv<typename std::remove_reference<_Tp>::type>::type, boost::json::value_ref>::value)>::value>::type* <anonymous> > void boost::json::detail::value_from_helper(boost::json::value&, T&&, boost::json::detail::priority_tag<4>)’  
   75 | value_from_helper(  
      | ^~~~~~~~~~~~~~~~~  
/usr/local/include/boost/json/detail/value_from.hpp:75:1: note:   template argument deduction/substitution failed:  
/usr/local/include/boost/json/detail/value_from.hpp:73:53: error: no type named ‘type’ in ‘struct std::enable_if<false, void>’  
   73 |     detail::value_constructible<T>::value>::type* = nullptr>  
      |                                                     ^~~~~~~  
/usr/local/include/boost/json/detail/value_from.hpp:87:1: note: candidate: ‘template<class T, typename std::enable_if<std::is_same<typename std::remove_cv<typename std::remove_reference<_Tp>::type>::type, std::nullptr_t>::value>::type* <anonymous> > void boost::json::detail::value_from_helper(boost::json::value&, T&&, boost::json::detail::priority_tag<4>)’  
   87 | value_from_helper(  
      | ^~~~~~~~~~~~~~~~~  
/usr/local/include/boost/json/detail/value_from.hpp:87:1: note:   template argument deduction/substitution failed:  
/usr/local/include/boost/json/detail/value_from.hpp:85:42: error: no type named ‘type’ in ‘struct std::enable_if<false, void>’  
   85 |         std::nullptr_t>::value>::type* = nullptr>  
      |                                          ^~~~~~~  
/usr/local/include/boost/json/detail/value_from.hpp:109:1: note: candidate: ‘template<class T, typename std::enable_if<((std::is_constructible<typename std::remove_cv<typename std::remove_reference<_Tp>::type>::type, const char*, long unsigned int>::value && std::is_convertible<decltype (declval<T&>().data()), const char*>::value) && std::is_integral<decltype (declval<T&>().size())>::value)>::type* <anonymous> > void boost::json::detail::value_from_helper(boost::json::value&, T&&, boost::json::detail::priority_tag<3>)’  
  109 | value_from_helper(  
      | ^~~~~~~~~~~~~~~~~  
/usr/local/include/boost/json/detail/value_from.hpp:109:1: note:   template argument deduction/substitution failed:  
/usr/local/include/boost/json/detail/value_from.hpp:105:53: error: ‘const struct boost::json::object_kind_t’ has no member named ‘data’  
  105 |     std::is_convertible<decltype(std::declval<T&>().data()), const char*>::value &&  
      |                                  ~~~~~~~~~~~~~~~~~~~^~~~  
/usr/local/include/boost/json/detail/value_from.hpp:106:50: error: ‘const struct boost::json::object_kind_t’ has no member named ‘size’  
  106 |     std::is_integral<decltype(std::declval<T&>().size())>::value  
      |                               ~~~~~~~~~~~~~~~~~~~^~~~  
/usr/local/include/boost/json/detail/value_from.hpp:126:1: note: candidate: ‘template<class T, typename std::enable_if<((boost::json::detail::map_traits<T>::has_unique_keys && boost::json::has_value_from<typename boost::json::detail::map_traits<T>::pair_value_type>::value) && std::is_convertible<typename boost::json::detail::map_traits<T>::pair_key_type, boost::core::basic_string_view<char> >::value)>::type* <anonymous> > void boost::json::detail::value_from_helper(boost::json::value&, T&&, boost::json::detail::priority_tag<2>)’  
  126 | value_from_helper(  
      | ^~~~~~~~~~~~~~~~~  
/usr/local/include/boost/json/detail/value_from.hpp:126:1: note:   template argument deduction/substitution failed:  
/usr/local/include/boost/json/detail/value_from.hpp:122:66: error: no type named ‘pair_value_type’ in ‘struct boost::json::detail::map_traits<const boost::json::object_kind_t&, void>’  
  122 |         has_value_from<typename map_traits<T>::pair_value_type>::value &&  
      |                                                                  ^~~~~  
/usr/local/include/boost/json/detail/value_from.hpp:124:23: error: no type named ‘pair_key_type’ in ‘struct boost::json::detail::map_traits<const boost::json::object_kind_t&, void>’  
  124 |         string_view>::value>::type* = nullptr>  
      |                       ^~~~~  
/usr/local/include/boost/json/detail/value_from.hpp:145:1: note: candidate: ‘template<class T, typename std::enable_if<boost::json::has_value_from<typename boost::json::detail::container_traits<T>::value_type>::value>::type* <anonymous> > void boost::json::detail::value_from_helper(boost::json::value&, T&&, boost::json::detail::priority_tag<1>)’  
  145 | value_from_helper(  
      | ^~~~~~~~~~~~~~~~~  
/usr/local/include/boost/json/detail/value_from.hpp:145:1: note:   template argument deduction/substitution failed:  
/usr/local/include/boost/json/detail/value_from.hpp:143:38: error: no type named ‘value_type’ in ‘struct boost::json::detail::container_traits<const boost::json::object_kind_t&, void>’  
  143 |         value_type>::value>::type* = nullptr>  
      |                                      ^~~~~~~  
/usr/local/include/boost/json/detail/value_from.hpp:161:1: note: candidate: ‘template<class T, typename std::enable_if<(std::tuple_size<typename std::remove_cv<typename std::remove_reference<_Tp>::type>::type>::value > 0)>::type* <anonymous> > void boost::json::detail::value_from_helper(boost::json::value&, T&&, boost::json::detail::priority_tag<0>)’  
  161 | value_from_helper(  
      | ^~~~~~~~~~~~~~~~~  
/usr/local/include/boost/json/detail/value_from.hpp:161:1: note:   template argument deduction/substitution failed:  
/usr/local/include/boost/json/detail/value_from.hpp:159:40: error: incomplete type ‘std::tuple_size<boost::json::object_kind_t>’ used in nested name specifier  
  159 |     (std::tuple_size<remove_cvref<T>>::value > 0)>::type* = nullptr>  
      |                                        ^~~~~  
```

---

## Comment 1

> Username: vinniefalco  
> Created at: 2022-12-26 17:04:54 UTC  
> Updated at: 2022-12-26 17:05:18 UTC  
> Url: https://github.com/boostorg/json/issues/826#issuecomment-1365287600  

This code:  
```  
boost::json::value jv{ boost::json::object_kind };  
```  
  
Fails on VS2019 and Boost 1.80.0:  
```  
error C2672: 'boost::json::detail::value_from_impl': no matching overloaded function found  
```  
  
Compiles on VS2019 and Boost 1.81.0  
Compiles on VS2019 and the tip of develop

---

## Comment 2

> Username: matty0ung  
> Created at: 2022-12-26 18:33:55 UTC  
> Url: https://github.com/boostorg/json/issues/826#issuecomment-1365351795  

Thanks for the quick reply.  You're right, I was mixed up about what version of Boost I had -- only 1.80.  (Am compiling on several platforms and some of them are 1.81!)

---

## Comment 3

> Username: vinniefalco  
> Created at: 2022-12-26 19:12:14 UTC  
> Url: https://github.com/boostorg/json/issues/826#issuecomment-1365371226  

\phew :)
