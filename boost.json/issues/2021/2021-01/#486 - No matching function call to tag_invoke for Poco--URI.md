# #486 - No matching function call to tag_invoke for Poco::URI [Closed]

> Username: justinjereza  
> Created at: 2021-01-20 17:39:34 UTC  
> Updated at: 2021-01-20 18:32:49 UTC  
> Closed at: 2021-01-20 18:32:49 UTC  
> Url: https://github.com/boostorg/json/issues/486  

Fedora 33 / GCC 10.2.1 C++20 / Conan Boost 1.75 2d040665a7cb2820e00fe685a77c8169fcc3b764  
  
Despite `Poco::URI tag_invoke(json::value_to_tag<Poco::URI>, const json::value&)` being defined, `json::value_to<Poco::URI>()` is not able to find it. I am not sure if this is unique to this type or if there are other similar problems.  
  
Test case:  
```  
#include <string>  
#include <iostream>  
#include <Poco/URI.h>  
#include <boost/json/src.hpp>  
  
namespace json = boost::json;  
  
template<typename T>  
void jsonExtract(const boost::json::object& o, T& t, const std::string_view&& k) {  
    t = boost::json::value_to<T>(o.at(k));  
}  
  
Poco::URI tag_invoke(json::value_to_tag<Poco::URI>, const json::value& v) {  
    return Poco::URI{v.as_string().c_str()};  
}  
  
struct Data {  
    int id;  
    Poco::URI uri;  
};  
  
Data tag_invoke(json::value_to_tag<Data>, const json::value& v) {  
    Data r{};  
    const json::object o{v.as_object()};  
    jsonExtract(o, r.id, "id");  
    // jsonExtract(o, r.uri, "url"); // This does not work despite definition of tag_invoke for Poco:::URI above.  
    r.uri = Poco::URI{o.at("url").as_string().c_str()};  
    return r;  
    // return Data { // Does not work either.  
    //     json::value_to<int>(o.at("id")),  
    //     json::value_to<Poco::URI>(o.at("url"))  
    // };  
}  
  
int main() {  
    auto&& v = json::parse("{\"id\": 42,\"url\": \"https://google.com/\"}");  
    auto d = json::value_to<Data>(v);  
      
    std::cout  
        << "ID: " << d.id << "\n"  
        << "URI: " << d.uri.toString() << "\n";  
    return 0;  
}  
```  
  
Compiler error:  
```  
In file included from /root/.conan/data/boost/1.75.0/_/_/package/2d040665a7cb2820e00fe685a77c8169fcc3b764/include/boost/json.hpp:40,  
                 from /root/.conan/data/boost/1.75.0/_/_/package/2d040665a7cb2820e00fe685a77c8169fcc3b764/include/boost/json/src.hpp:27,  
                 from /root/test-cases/JsonUri.cxx:4:  
/root/.conan/data/boost/1.75.0/_/_/package/2d040665a7cb2820e00fe685a77c8169fcc3b764/include/boost/json/value_to.hpp: In instantiation of ‘T boost::json::standalone::value_to(const U&) [with T = Poco::URI; U = boost::json::standalone::value; typename std::enable_if<((! std::is_reference< <template-parameter-1-1> >::value) && std::is_same<U, boost::json::standalone::value>::value)>::type* <anonymous> = 0]’:  
/root/test-cases/JsonUri.cxx:10:33:   required from ‘void jsonExtract(const boost::json::standalone::object&, T&, const string_view&&) [with T = Poco::URI; std::string_view = std::basic_string_view<char>]’  
/root/test-cases/JsonUri.cxx:26:32:   required from here  
/root/.conan/data/boost/1.75.0/_/_/package/2d040665a7cb2820e00fe685a77c8169fcc3b764/include/boost/json/value_to.hpp:89:33: error: no matching function for call to ‘value_to_impl(boost::json::standalone::value_to_tag<Poco::URI>, const boost::json::standalone::value&)’  
   89 |     return detail::value_to_impl(  
      |            ~~~~~~~~~~~~~~~~~~~~~^  
   90 |         value_to_tag<typename std::remove_cv<T>::type>(), jv);  
      |         ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
In file included from /root/.conan/data/boost/1.75.0/_/_/package/2d040665a7cb2820e00fe685a77c8169fcc3b764/include/boost/json/value_to.hpp:16,  
                 from /root/.conan/data/boost/1.75.0/_/_/package/2d040665a7cb2820e00fe685a77c8169fcc3b764/include/boost/json.hpp:40,  
                 from /root/.conan/data/boost/1.75.0/_/_/package/2d040665a7cb2820e00fe685a77c8169fcc3b764/include/boost/json/src.hpp:27,  
                 from /root/test-cases/JsonUri.cxx:4:  
/root/.conan/data/boost/1.75.0/_/_/package/2d040665a7cb2820e00fe685a77c8169fcc3b764/include/boost/json/detail/value_to.hpp:185:1: note: candidate: ‘template<class T, boost::json::standalone::detail::void_t<decltype (boost::json::standalone::detail::tag_invoke(declval<boost::json::standalone::value_to_tag<T>&>(), declval<const boost::json::standalone::value&>()))>* <anonymous> > T boost::json::standalone::detail::value_to_impl(boost::json::standalone::value_to_tag<T>, const boost::json::standalone::value&)’  
  185 | value_to_impl(  
      | ^~~~~~~~~~~~~  
/root/.conan/data/boost/1.75.0/_/_/package/2d040665a7cb2820e00fe685a77c8169fcc3b764/include/boost/json/detail/value_to.hpp:185:1: note:   template argument deduction/substitution failed:  
/root/.conan/data/boost/1.75.0/_/_/package/2d040665a7cb2820e00fe685a77c8169fcc3b764/include/boost/json/detail/value_to.hpp:182:24: error: no matching function for call to ‘tag_invoke(boost::json::standalone::value_to_tag<Poco::URI>&, const boost::json::standalone::value&)’  
  182 |     decltype(tag_invoke(std::declval<value_to_tag<T>&>(),  
      |              ~~~~~~~~~~^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
  183 |         std::declval<const value&>()))>* = nullptr>  
      |         ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
In file included from /root/.conan/data/boost/1.75.0/_/_/package/2d040665a7cb2820e00fe685a77c8169fcc3b764/include/boost/json/value_from.hpp:16,  
                 from /root/.conan/data/boost/1.75.0/_/_/package/2d040665a7cb2820e00fe685a77c8169fcc3b764/include/boost/json/impl/value_ref.hpp:13,  
                 from /root/.conan/data/boost/1.75.0/_/_/package/2d040665a7cb2820e00fe685a77c8169fcc3b764/include/boost/json/value.hpp:3924,  
                 from /root/.conan/data/boost/1.75.0/_/_/package/2d040665a7cb2820e00fe685a77c8169fcc3b764/include/boost/json/array.hpp:1744,  
                 from /root/.conan/data/boost/1.75.0/_/_/package/2d040665a7cb2820e00fe685a77c8169fcc3b764/include/boost/json.hpp:15,  
                 from /root/.conan/data/boost/1.75.0/_/_/package/2d040665a7cb2820e00fe685a77c8169fcc3b764/include/boost/json/src.hpp:27,  
                 from /root/test-cases/JsonUri.cxx:4:  
/root/.conan/data/boost/1.75.0/_/_/package/2d040665a7cb2820e00fe685a77c8169fcc3b764/include/boost/json/detail/value_from.hpp:60:1: note: candidate: ‘template<class T, typename std::enable_if<std::integral_constant<bool, (((((((std::is_same<typename std::remove_cv<typename std::remove_reference<_Tp>::type>::type, boost::json::standalone::value>::value || std::is_same<typename std::remove_cv<typename std::remove_reference<_Tp>::type>::type, boost::json::standalone::object>::value) || std::is_same<typename std::remove_cv<typename std::remove_reference<_Tp>::type>::type, boost::json::standalone::array>::value) || std::is_same<typename std::remove_cv<typename std::remove_reference<_Tp>::type>::type, boost::json::standalone::string>::value) || std::is_same<typename std::remove_cv<typename std::remove_reference<_Tp>::type>::type, std::basic_string_view<char> >::value) || std::is_arithmetic<typename std::remove_cv<typename std::remove_reference<_Tp>::type>::type>::value) || std::is_same<typename std::remove_cv<typename std::remove_reference<_Tp>::type>::type, std::initializer_list<boost::json::standalone::value_ref> >::value) || std::is_same<typename std::remove_cv<typename std::remove_reference<_Tp>::type>::type, boost::json::standalone::value_ref>::value)>::value>::type* <anonymous> > void boost::json::standalone::detail::tag_invoke(boost::json::standalone::value_from_tag, boost::json::standalone::value&, T&&)’  
   60 | tag_invoke(  
      | ^~~~~~~~~~  
/root/.conan/data/boost/1.75.0/_/_/package/2d040665a7cb2820e00fe685a77c8169fcc3b764/include/boost/json/detail/value_from.hpp:60:1: note:   template argument deduction/substitution failed:  
In file included from /root/.conan/data/boost/1.75.0/_/_/package/2d040665a7cb2820e00fe685a77c8169fcc3b764/include/boost/json/value_to.hpp:16,  
                 from /root/.conan/data/boost/1.75.0/_/_/package/2d040665a7cb2820e00fe685a77c8169fcc3b764/include/boost/json.hpp:40,  
                 from /root/.conan/data/boost/1.75.0/_/_/package/2d040665a7cb2820e00fe685a77c8169fcc3b764/include/boost/json/src.hpp:27,  
                 from /root/test-cases/JsonUri.cxx:4:  
/root/.conan/data/boost/1.75.0/_/_/package/2d040665a7cb2820e00fe685a77c8169fcc3b764/include/boost/json/detail/value_to.hpp:182:24: note:   candidate expects 3 arguments, 2 provided  
  182 |     decltype(tag_invoke(std::declval<value_to_tag<T>&>(),  
      |              ~~~~~~~~~~^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
  183 |         std::declval<const value&>()))>* = nullptr>  
      |         ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
In file included from /root/.conan/data/boost/1.75.0/_/_/package/2d040665a7cb2820e00fe685a77c8169fcc3b764/include/boost/json/value_from.hpp:16,  
                 from /root/.conan/data/boost/1.75.0/_/_/package/2d040665a7cb2820e00fe685a77c8169fcc3b764/include/boost/json/impl/value_ref.hpp:13,  
                 from /root/.conan/data/boost/1.75.0/_/_/package/2d040665a7cb2820e00fe685a77c8169fcc3b764/include/boost/json/value.hpp:3924,  
                 from /root/.conan/data/boost/1.75.0/_/_/package/2d040665a7cb2820e00fe685a77c8169fcc3b764/include/boost/json/array.hpp:1744,  
                 from /root/.conan/data/boost/1.75.0/_/_/package/2d040665a7cb2820e00fe685a77c8169fcc3b764/include/boost/json.hpp:15,  
                 from /root/.conan/data/boost/1.75.0/_/_/package/2d040665a7cb2820e00fe685a77c8169fcc3b764/include/boost/json/src.hpp:27,  
                 from /root/test-cases/JsonUri.cxx:4:  
/root/.conan/data/boost/1.75.0/_/_/package/2d040665a7cb2820e00fe685a77c8169fcc3b764/include/boost/json/detail/value_from.hpp:72:1: note: candidate: ‘template<class T, typename std::enable_if<std::is_same<typename std::remove_cv<typename std::remove_reference<_Tp>::type>::type, std::nullptr_t>::value>::type* <anonymous> > void boost::json::standalone::detail::tag_invoke(boost::json::standalone::value_from_tag, boost::json::standalone::value&, T&&)’  
   72 | tag_invoke(  
      | ^~~~~~~~~~  
/root/.conan/data/boost/1.75.0/_/_/package/2d040665a7cb2820e00fe685a77c8169fcc3b764/include/boost/json/detail/value_from.hpp:72:1: note:   template argument deduction/substitution failed:  
In file included from /root/.conan/data/boost/1.75.0/_/_/package/2d040665a7cb2820e00fe685a77c8169fcc3b764/include/boost/json/value_to.hpp:16,  
                 from /root/.conan/data/boost/1.75.0/_/_/package/2d040665a7cb2820e00fe685a77c8169fcc3b764/include/boost/json.hpp:40,  
                 from /root/.conan/data/boost/1.75.0/_/_/package/2d040665a7cb2820e00fe685a77c8169fcc3b764/include/boost/json/src.hpp:27,  
                 from /root/test-cases/JsonUri.cxx:4:  
/root/.conan/data/boost/1.75.0/_/_/package/2d040665a7cb2820e00fe685a77c8169fcc3b764/include/boost/json/detail/value_to.hpp:182:24: note:   candidate expects 3 arguments, 2 provided  
  182 |     decltype(tag_invoke(std::declval<value_to_tag<T>&>(),  
      |              ~~~~~~~~~~^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
  183 |         std::declval<const value&>()))>* = nullptr>  
      |         ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
In file included from /root/.conan/data/boost/1.75.0/_/_/package/2d040665a7cb2820e00fe685a77c8169fcc3b764/include/boost/json/value_from.hpp:16,  
                 from /root/.conan/data/boost/1.75.0/_/_/package/2d040665a7cb2820e00fe685a77c8169fcc3b764/include/boost/json/impl/value_ref.hpp:13,  
                 from /root/.conan/data/boost/1.75.0/_/_/package/2d040665a7cb2820e00fe685a77c8169fcc3b764/include/boost/json/value.hpp:3924,  
                 from /root/.conan/data/boost/1.75.0/_/_/package/2d040665a7cb2820e00fe685a77c8169fcc3b764/include/boost/json/array.hpp:1744,  
                 from /root/.conan/data/boost/1.75.0/_/_/package/2d040665a7cb2820e00fe685a77c8169fcc3b764/include/boost/json.hpp:15,  
                 from /root/.conan/data/boost/1.75.0/_/_/package/2d040665a7cb2820e00fe685a77c8169fcc3b764/include/boost/json/src.hpp:27,  
                 from /root/test-cases/JsonUri.cxx:4:  
/root/.conan/data/boost/1.75.0/_/_/package/2d040665a7cb2820e00fe685a77c8169fcc3b764/include/boost/json/detail/value_from.hpp:163:1: note: candidate: ‘template<class T, boost::json::standalone::detail::void_t<typename std::enable_if<((! std::integral_constant<bool, (((((((std::is_same<typename std::remove_cv<typename std::remove_reference<_Tp>::type>::type, boost::json::standalone::value>::value || std::is_same<typename std::remove_cv<typename std::remove_reference<_Tp>::type>::type, boost::json::standalone::object>::value) || std::is_same<typename std::remove_cv<typename std::remove_reference<_Tp>::type>::type, boost::json::standalone::array>::value) || std::is_same<typename std::remove_cv<typename std::remove_reference<_Tp>::type>::type, boost::json::standalone::string>::value) || std::is_same<typename std::remove_cv<typename std::remove_reference<_Tp>::type>::type, std::basic_string_view<char, std::char_traits<char> > >::value) || std::is_arithmetic<typename std::remove_cv<typename std::remove_reference<_Tp>::type>::type>::value) || std::is_same<typename std::remove_cv<typename std::remove_reference<_Tp>::type>::type, std::initializer_list<boost::json::standalone::value_ref> >::value) || std::is_same<typename std::remove_cv<typename std::remove_reference<_Tp>::type>::type, boost::json::standalone::value_ref>::value)>::value) && (! std::is_same<typename std::remove_cv<typename std::remove_reference<_Tp>::type>::type, std::nullptr_t>::value)), void>::type, decltype (boost::json::standalone::detail::value_from_generic(declval<boost::json::standalone::value&>(), declval<T&&>(), boost::json::standalone::detail::priority_tag<3>()))>* <anonymous> > void boost::json::standalone::detail::tag_invoke(boost::json::standalone::value_from_tag, boost::json::standalone::value&, T&&)’  
  163 | tag_invoke(  
      | ^~~~~~~~~~  
/root/.conan/data/boost/1.75.0/_/_/package/2d040665a7cb2820e00fe685a77c8169fcc3b764/include/boost/json/detail/value_from.hpp:163:1: note:   template argument deduction/substitution failed:  
In file included from /root/.conan/data/boost/1.75.0/_/_/package/2d040665a7cb2820e00fe685a77c8169fcc3b764/include/boost/json/value_to.hpp:16,  
                 from /root/.conan/data/boost/1.75.0/_/_/package/2d040665a7cb2820e00fe685a77c8169fcc3b764/include/boost/json.hpp:40,  
                 from /root/.conan/data/boost/1.75.0/_/_/package/2d040665a7cb2820e00fe685a77c8169fcc3b764/include/boost/json/src.hpp:27,  
                 from /root/test-cases/JsonUri.cxx:4:  
/root/.conan/data/boost/1.75.0/_/_/package/2d040665a7cb2820e00fe685a77c8169fcc3b764/include/boost/json/detail/value_to.hpp:182:24: note:   candidate expects 3 arguments, 2 provided  
  182 |     decltype(tag_invoke(std::declval<value_to_tag<T>&>(),  
      |              ~~~~~~~~~~^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
  183 |         std::declval<const value&>()))>* = nullptr>  
      |         ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
/root/.conan/data/boost/1.75.0/_/_/package/2d040665a7cb2820e00fe685a77c8169fcc3b764/include/boost/json/detail/value_to.hpp:41:1: note: candidate: ‘boost::json::standalone::value boost::json::standalone::detail::tag_invoke(boost::json::standalone::value_to_tag<boost::json::standalone::value>, const boost::json::standalone::value&)’  
   41 | tag_invoke(  
      | ^~~~~~~~~~  
/root/.conan/data/boost/1.75.0/_/_/package/2d040665a7cb2820e00fe685a77c8169fcc3b764/include/boost/json/detail/value_to.hpp:42:5: note:   no known conversion for argument 1 from ‘value_to_tag<Poco::URI>’ to ‘value_to_tag<boost::json::standalone::value>’  
   42 |     value_to_tag<value>,  
      |     ^~~~~~~~~~~~~~~~~~~  
/root/.conan/data/boost/1.75.0/_/_/package/2d040665a7cb2820e00fe685a77c8169fcc3b764/include/boost/json/detail/value_to.hpp:51:1: note: candidate: ‘boost::json::standalone::object boost::json::standalone::detail::tag_invoke(boost::json::standalone::value_to_tag<boost::json::standalone::object>, const boost::json::standalone::value&)’  
   51 | tag_invoke(  
      | ^~~~~~~~~~  
/root/.conan/data/boost/1.75.0/_/_/package/2d040665a7cb2820e00fe685a77c8169fcc3b764/include/boost/json/detail/value_to.hpp:52:5: note:   no known conversion for argument 1 from ‘value_to_tag<Poco::URI>’ to ‘value_to_tag<boost::json::standalone::object>’  
   52 |     value_to_tag<object>,  
      |     ^~~~~~~~~~~~~~~~~~~~  
/root/.conan/data/boost/1.75.0/_/_/package/2d040665a7cb2820e00fe685a77c8169fcc3b764/include/boost/json/detail/value_to.hpp:61:1: note: candidate: ‘boost::json::standalone::array boost::json::standalone::detail::tag_invoke(boost::json::standalone::value_to_tag<boost::json::standalone::array>, const boost::json::standalone::value&)’  
   61 | tag_invoke(  
      | ^~~~~~~~~~  
/root/.conan/data/boost/1.75.0/_/_/package/2d040665a7cb2820e00fe685a77c8169fcc3b764/include/boost/json/detail/value_to.hpp:62:5: note:   no known conversion for argument 1 from ‘value_to_tag<Poco::URI>’ to ‘value_to_tag<boost::json::standalone::array>’  
   62 |     value_to_tag<array>,  
      |     ^~~~~~~~~~~~~~~~~~~  
/root/.conan/data/boost/1.75.0/_/_/package/2d040665a7cb2820e00fe685a77c8169fcc3b764/include/boost/json/detail/value_to.hpp:71:1: note: candidate: ‘boost::json::standalone::string boost::json::standalone::detail::tag_invoke(boost::json::standalone::value_to_tag<boost::json::standalone::string>, const boost::json::standalone::value&)’  
   71 | tag_invoke(  
      | ^~~~~~~~~~  
/root/.conan/data/boost/1.75.0/_/_/package/2d040665a7cb2820e00fe685a77c8169fcc3b764/include/boost/json/detail/value_to.hpp:72:5: note:   no known conversion for argument 1 from ‘value_to_tag<Poco::URI>’ to ‘value_to_tag<boost::json::standalone::string>’  
   72 |     value_to_tag<string>,  
      |     ^~~~~~~~~~~~~~~~~~~~  
/root/.conan/data/boost/1.75.0/_/_/package/2d040665a7cb2820e00fe685a77c8169fcc3b764/include/boost/json/detail/value_to.hpp:81:1: note: candidate: ‘bool boost::json::standalone::detail::tag_invoke(boost::json::standalone::value_to_tag<bool>, const boost::json::standalone::value&)’  
   81 | tag_invoke(  
      | ^~~~~~~~~~  
/root/.conan/data/boost/1.75.0/_/_/package/2d040665a7cb2820e00fe685a77c8169fcc3b764/include/boost/json/detail/value_to.hpp:82:5: note:   no known conversion for argument 1 from ‘value_to_tag<Poco::URI>’ to ‘value_to_tag<bool>’  
   82 |     value_to_tag<bool>,  
      |     ^~~~~~~~~~~~~~~~~~  
/root/.conan/data/boost/1.75.0/_/_/package/2d040665a7cb2820e00fe685a77c8169fcc3b764/include/boost/json/detail/value_to.hpp:92:1: note: candidate: ‘template<class T, typename std::enable_if<std::is_arithmetic<_Tp>::value>::type* <anonymous> > T boost::json::standalone::detail::tag_invoke(boost::json::standalone::value_to_tag<T>, const boost::json::standalone::value&)’  
   92 | tag_invoke(  
      | ^~~~~~~~~~  
/root/.conan/data/boost/1.75.0/_/_/package/2d040665a7cb2820e00fe685a77c8169fcc3b764/include/boost/json/detail/value_to.hpp:92:1: note:   template argument deduction/substitution failed:  
/root/.conan/data/boost/1.75.0/_/_/package/2d040665a7cb2820e00fe685a77c8169fcc3b764/include/boost/json/detail/value_to.hpp:90:44: error: no type named ‘type’ in ‘struct std::enable_if<false, void>’  
   90 |     std::is_arithmetic<T>::value>::type* = nullptr>  
      |                                            ^~~~~~~  
/root/.conan/data/boost/1.75.0/_/_/package/2d040665a7cb2820e00fe685a77c8169fcc3b764/include/boost/json/detail/value_to.hpp:168:1: note: candidate: ‘template<class T, boost::json::standalone::detail::void_t<typename std::enable_if<((! std::is_constructible<T, const boost::json::standalone::value&>::value) && (! std::is_arithmetic<_Tp>::value)), void>::type, decltype (value_to_generic<T>(declval<const boost::json::standalone::value&>(), boost::json::standalone::detail::priority_tag<2>()))>* <anonymous> > T boost::json::standalone::detail::tag_invoke(boost::json::standalone::value_to_tag<T>, const boost::json::standalone::value&)’  
  168 | tag_invoke(  
      | ^~~~~~~~~~  
/root/.conan/data/boost/1.75.0/_/_/package/2d040665a7cb2820e00fe685a77c8169fcc3b764/include/boost/json/detail/value_to.hpp:168:1: note:   template argument deduction/substitution failed:  
/root/.conan/data/boost/1.75.0/_/_/package/2d040665a7cb2820e00fe685a77c8169fcc3b764/include/boost/json/detail/value_to.hpp:165:24: error: no matching function for call to ‘value_to_generic<Poco::URI>(const boost::json::standalone::value&, boost::json::standalone::detail::priority_tag<2>)’  
  165 |     value_to_generic<T>(std::declval<const value&>(),  
      |     ~~~~~~~~~~~~~~~~~~~^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
  166 |         priority_tag<2>()))>* = nullptr>  
      |         ~~~~~~~~~~~~~~~~~~  
/root/.conan/data/boost/1.75.0/_/_/package/2d040665a7cb2820e00fe685a77c8169fcc3b764/include/boost/json/detail/value_to.hpp:111:1: note: candidate: ‘template<class T, typename std::enable_if<((std::is_constructible<T, const char*, long unsigned int>::value && std::is_convertible<decltype (declval<T&>().data()), const char*>::value) && std::is_integral<decltype (declval<T&>().size())>::value)>::type* <anonymous> > T boost::json::standalone::detail::value_to_generic(const boost::json::standalone::value&, boost::json::standalone::detail::priority_tag<2>)’  
  111 | value_to_generic(  
      | ^~~~~~~~~~~~~~~~  
/root/.conan/data/boost/1.75.0/_/_/package/2d040665a7cb2820e00fe685a77c8169fcc3b764/include/boost/json/detail/value_to.hpp:111:1: note:   template argument deduction/substitution failed:  
In file included from /root/.conan/data/boost/1.75.0/_/_/package/2d040665a7cb2820e00fe685a77c8169fcc3b764/include/boost/json/value_from.hpp:16,  
                 from /root/.conan/data/boost/1.75.0/_/_/package/2d040665a7cb2820e00fe685a77c8169fcc3b764/include/boost/json/impl/value_ref.hpp:13,  
                 from /root/.conan/data/boost/1.75.0/_/_/package/2d040665a7cb2820e00fe685a77c8169fcc3b764/include/boost/json/value.hpp:3924,  
                 from /root/.conan/data/boost/1.75.0/_/_/package/2d040665a7cb2820e00fe685a77c8169fcc3b764/include/boost/json/array.hpp:1744,  
                 from /root/.conan/data/boost/1.75.0/_/_/package/2d040665a7cb2820e00fe685a77c8169fcc3b764/include/boost/json.hpp:15,  
                 from /root/.conan/data/boost/1.75.0/_/_/package/2d040665a7cb2820e00fe685a77c8169fcc3b764/include/boost/json/src.hpp:27,  
                 from /root/test-cases/JsonUri.cxx:4:  
/root/.conan/data/boost/1.75.0/_/_/package/2d040665a7cb2820e00fe685a77c8169fcc3b764/include/boost/json/detail/value_from.hpp:90:53: error: ‘class Poco::URI’ has no member named ‘data’  
   90 |     std::is_convertible<decltype(std::declval<T&>().data()), const char*>::value &&  
      |                                  ~~~~~~~~~~~~~~~~~~~^~~~  
/root/.conan/data/boost/1.75.0/_/_/package/2d040665a7cb2820e00fe685a77c8169fcc3b764/include/boost/json/detail/value_from.hpp:91:50: error: ‘class Poco::URI’ has no member named ‘size’  
   91 |     std::is_integral<decltype(std::declval<T&>().size())>::value  
      |                               ~~~~~~~~~~~~~~~~~~~^~~~  
In file included from /root/.conan/data/boost/1.75.0/_/_/package/2d040665a7cb2820e00fe685a77c8169fcc3b764/include/boost/json/value_to.hpp:16,  
                 from /root/.conan/data/boost/1.75.0/_/_/package/2d040665a7cb2820e00fe685a77c8169fcc3b764/include/boost/json.hpp:40,  
                 from /root/.conan/data/boost/1.75.0/_/_/package/2d040665a7cb2820e00fe685a77c8169fcc3b764/include/boost/json/src.hpp:27,  
                 from /root/test-cases/JsonUri.cxx:4:  
/root/.conan/data/boost/1.75.0/_/_/package/2d040665a7cb2820e00fe685a77c8169fcc3b764/include/boost/json/detail/value_to.hpp:125:1: note: candidate: ‘template<class T, typename std::enable_if<(boost::json::standalone::has_value_to<typename boost::json::standalone::detail::map_traits<T>::pair_value_type>::value && std::is_constructible<typename boost::json::standalone::detail::map_traits<T>::pair_key_type, std::basic_string_view<char, std::char_traits<char> > >::value)>::type* <anonymous> > T boost::json::standalone::detail::value_to_generic(const boost::json::standalone::value&, boost::json::standalone::detail::priority_tag<1>)’  
  125 | value_to_generic(  
      | ^~~~~~~~~~~~~~~~  
/root/.conan/data/boost/1.75.0/_/_/package/2d040665a7cb2820e00fe685a77c8169fcc3b764/include/boost/json/detail/value_to.hpp:125:1: note:   template argument deduction/substitution failed:  
/root/.conan/data/boost/1.75.0/_/_/package/2d040665a7cb2820e00fe685a77c8169fcc3b764/include/boost/json/detail/value_to.hpp:121:60: error: no type named ‘pair_value_type’ in ‘struct boost::json::standalone::detail::map_traits<Poco::URI, void>’  
  121 |     has_value_to<typename map_traits<T>::pair_value_type>::value &&  
      |                                                            ^~~~~  
/root/.conan/data/boost/1.75.0/_/_/package/2d040665a7cb2820e00fe685a77c8169fcc3b764/include/boost/json/detail/value_to.hpp:123:19: error: no type named ‘pair_key_type’ in ‘struct boost::json::standalone::detail::map_traits<Poco::URI, void>’  
  123 |     string_view>::value>::type* = nullptr>  
      |                   ^~~~~  
/root/.conan/data/boost/1.75.0/_/_/package/2d040665a7cb2820e00fe685a77c8169fcc3b764/include/boost/json/detail/value_to.hpp:147:1: note: candidate: ‘template<class T, typename std::enable_if<boost::json::standalone::has_value_to<typename boost::json::standalone::detail::container_traits<T>::value_type>::value>::type* <anonymous> > T boost::json::standalone::detail::value_to_generic(const boost::json::standalone::value&, boost::json::standalone::detail::priority_tag<0>)’  
  147 | value_to_generic(  
      | ^~~~~~~~~~~~~~~~  
/root/.conan/data/boost/1.75.0/_/_/package/2d040665a7cb2820e00fe685a77c8169fcc3b764/include/boost/json/detail/value_to.hpp:147:1: note:   template argument deduction/substitution failed:  
/root/.conan/data/boost/1.75.0/_/_/package/2d040665a7cb2820e00fe685a77c8169fcc3b764/include/boost/json/detail/value_to.hpp:145:38: error: no type named ‘value_type’ in ‘struct boost::json::standalone::detail::container_traits<Poco::URI, void>’  
  145 |         value_type>::value>::type* = nullptr>  
      |                                      ^~~~~~~  
gmake[2]: *** [test-cases/CMakeFiles/json-uri.dir/build.make:82: test-cases/CMakeFiles/json-uri.dir/JsonUri.cxx.o] Error 1  
gmake[1]: *** [CMakeFiles/Makefile2:198: test-cases/CMakeFiles/json-uri.dir/all] Error 2  
gmake: *** [Makefile:103: all] Error 2  
```

---

## Comment 1

> Username: grisumbras  
> Created at: 2021-01-20 18:05:56 UTC  
> Updated at: 2021-01-20 18:07:14 UTC  
> Url: https://github.com/boostorg/json/issues/486#issuecomment-763831638  

`tag_invoke` overloads are supposed to be found via ADL. This means, you have to put them in one associated namespace for function's arguments. In this case it is either `boost::json` or `Poco`.

---

## Comment 2

> Username: justinjereza  
> Created at: 2021-01-20 18:32:49 UTC  
> Url: https://github.com/boostorg/json/issues/486#issuecomment-763847376  

I see. I did not think about ADL at all. Putting them all in the same namespace was indeed an easy fix. Thank you.
