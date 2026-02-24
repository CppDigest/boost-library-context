# #55 - write_json fails when using basic_ptree with other string types [Open]

> Username: ghost  
> Created at: 2020-07-12 10:38:14 UTC  
> Updated at: 2025-02-08 23:05:39 UTC  
> Url: https://github.com/boostorg/property_tree/issues/55  

Good morning,   
  
I was working on a project, I would like to use basic_ptree with strings of non standard type (see T1.cpp attached toy example).  
  
While read_json works smootly, write_json fails (see compiler output T1.error.txt).   
  
I checked the code and, apparently, an easy fix could enable this feature (see write.hpp which I revised for your convenience, original lines of code are commented, those inserted are marked with an eol comment "suggested").   
  
Thank you for your time,  
Marco.  
  
[write.hpp.txt](https://github.com/boostorg/property_tree/files/4908338/write.hpp.txt)  
[T1.cpp.txt](https://github.com/boostorg/property_tree/files/4908339/T1.cpp.txt)  
[T1.error.txt](https://github.com/boostorg/property_tree/files/4908340/T1.error.txt)

---

## Comment 1

> Username: ljluestc  
> Created at: 2025-02-08 23:05:37 UTC  
> Url: https://github.com/boostorg/property_tree/issues/55#issuecomment-2645975960  

```  
  
#ifndef BOOST_PROPERTY_TREE_DETAIL_WRITE_HPP  
#define BOOST_PROPERTY_TREE_DETAIL_WRITE_HPP  
  
#include <boost/property_tree/ptree.hpp>  
#include <boost/property_tree/detail/file_parser_error.hpp>  
#include <boost/property_tree/detail/xml_parser_write.hpp>  
#include <boost/property_tree/detail/json_parser_write.hpp>  
#include <boost/property_tree/detail/info_parser_write.hpp>  
#include <boost/property_tree/detail/ini_parser_write.hpp>  
#include <boost/type_traits/is_same.hpp>  
#include <boost/utility/enable_if.hpp>  
#include <boost/locale.hpp>  
#include <string>  
#include <ostream>  
  
namespace boost { namespace property_tree { namespace detail {  
  
// Helper function to convert non-standard string types to UTF-8  
template <typename Str>  
typename boost::enable_if<!boost::is_same<Str, std::string>, std::string>::type  
to_utf8(const Str& str) {  
    return boost::locale::conv::utf_to_utf<char>(str);  
}  
  
template <typename Str>  
typename boost::enable_if<boost::is_same<Str, std::string>, const Str&>::type  
to_utf8(const Str& str) {  
    return str;  
}  
  
// Modified write_json function  
template <typename Str, typename Ptree>  
void write_json(std::basic_ostream<typename Str::value_type>& stream,  
                const Ptree& pt,  
                bool pretty = false)  
{  
    // Convert non-standard string types to UTF-8  
    std::string utf8_str = to_utf8(pt.template get_value<Str>());  
  
    // Write the JSON to the stream  
    json_parser::write_json(stream, utf8_str, pretty);  
}  
  
}}} // namespace boost::property_tree::detail  
  
#endif // BOOST_PROPERTY_TREE_DETAIL_WRITE_HPP  
```
