# #41 - add an interface to pass file name (or a symbolic name for the input stream) to the parser for exceptions [Open]

> Username: bubnikv  
> Created at: 2019-06-19 07:24:54 UTC  
> Updated at: 2025-02-08 23:52:15 UTC  
> Url: https://github.com/boostorg/property_tree/issues/41  

When parsing a faulty file or stream, exceptions are often thrown with an empty file names, which are then replaced with <unspecified file> in the file_parser_error() constructor. It would help our project a lot, if there was a way to pass the name of the file or a symbolic name of the input stream to the parser functions, so that this field would be filled in in the exception and shown to the user.

---

## Comment 1

> Username: ljluestc  
> Created at: 2025-02-08 23:52:14 UTC  
> Url: https://github.com/boostorg/property_tree/issues/41#issuecomment-2645986747  

```  
  
#ifndef BOOST_PROPERTY_TREE_JSON_PARSER_HPP  
#define BOOST_PROPERTY_TREE_JSON_PARSER_HPP  
  
#include <boost/property_tree/ptree.hpp>  
#include <boost/property_tree/detail/file_parser_error.hpp>  
#include <boost/utility/enable_if.hpp>  
#include <boost/type_traits/is_same.hpp>  
#include <string>  
#include <istream>  
  
namespace boost { namespace property_tree { namespace json_parser {  
  
// Original read_json function  
template <typename Ptree>  
void read_json(std::basic_istream<typename Ptree::key_type::value_type>& stream, Ptree& pt);  
  
// New read_json function with filename support  
template <typename Ptree>  
void read_json(std::basic_istream<typename Ptree::key_type::value_type>& stream, Ptree& pt, const std::string& filename) {  
    try {  
        read_json(stream, pt);  
    } catch (const json_parser_error& e) {  
        // Re-throw the exception with the filename  
        throw json_parser_error(e.message(), filename, e.line());  
    }  
}  
  
// New read_json function for files  
template <typename Ptree>  
void read_json(const std::string& filename, Ptree& pt) {  
    std::ifstream stream(filename);  
    if (!stream) {  
        throw json_parser_error("Failed to open file", filename, 0);  
    }  
    read_json(stream, pt, filename);  
}  
  
}}} // namespace boost::property_tree::json_parser  
  
#endif // BOOST_PROPERTY_TREE_JSON_PARSER_HPP  
```
