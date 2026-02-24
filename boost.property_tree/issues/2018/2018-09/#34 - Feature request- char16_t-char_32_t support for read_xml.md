# #34 - Feature request: char16_t/char_32_t support for read_xml [Open]

> Username: Dani-Hub  
> Created at: 2018-09-15 14:13:36 UTC  
> Updated at: 2025-02-08 22:56:03 UTC  
> Url: https://github.com/boostorg/property_tree/issues/34  

Similar to [this issue](https://svn.boost.org/trac10/ticket/9919) but less ambitious, I would like to suggest to provide minimum support for `char16_t` and` char32_t` to read XML, basically an extension of [a previous BOM support issue](https://svn.boost.org/trac10/ticket/1678). For example, the following code snippet does currently not compile:  
```  
#include <boost/property_tree/ptree.hpp>  
#include <boost/property_tree/xml_parser.hpp>  
  
int main()  
{  
  boost::property_tree::basic_ptree<std::u16string, std::u16string> pt;  
  std::string filename("utf16.xml");   
  read_xml(filename, pt);  
}  
```  
producing the following compile error:  
```  
1>c:\projects\thirdparty\boost_1_68_0\boost\property_tree\detail\rapidxml.hpp(1374): error C2664: 'void boost::property_tree::detail::rapidxml::xml_document<Ch>::parse_bom<3072>(wchar_t *&)': cannot convert argument 1 from 'Ch *' to 'char *&'  
1>        with  
1>        [  
1>            Ch=Ch  
1>        ]  
1>c:\projects\thirdparty\boost_1_68_0\boost\property_tree\detail\xml_parser_read_rapidxml.hpp(116): note: see reference to function template instantiation 'void boost::property_tree::detail::rapidxml::xml_document<Ch>::parse<3072>(Ch *)' being compiled  
1>        with  
1>        [  
1>            Ch=Ch  
1>        ]  
1>c:\projects\thirdparty\boost_1_68_0\boost\property_tree\detail\xml_parser_read_rapidxml.hpp(116): note: see reference to function template instantiation 'void boost::property_tree::detail::rapidxml::xml_document<Ch>::parse<3072>(Ch *)' being compiled  
1>        with  
1>        [  
1>            Ch=Ch  
1>        ]  
1>c:\projects\thirdparty\boost_1_68_0\boost\property_tree\xml_parser.hpp(85): note: see reference to function template instantiation 'void boost::property_tree::xml_parser::read_xml_internal<Ptree>(std::basic_istream<char16_t,std::char_traits<char16_t>> &,Ptree &,int,const std::string &)' being compiled  
1>        with  
1>        [  
1>            Ptree=boost::property_tree::basic_ptree<std::u16string,std::u16string,std::less<std::u16string>>  
1>        ]  
[...]  
1>Done building project "bdal-playground.vcxproj" -- FAILED.  
```  
First inspection indicates that there are only missing specializations of the `parse_bom` member template for types `char16_t` and `char_32_t`.  
  
I'm willing to make a corresponding pull request, but opened this issue first to get feedback on this idea.

---

## Comment 1

> Username: ljluestc  
> Created at: 2025-02-08 22:56:02 UTC  
> Url: https://github.com/boostorg/property_tree/issues/34#issuecomment-2645973564  

```  
  
#include <boost/property_tree/ptree.hpp>  
#include <boost/property_tree/xml_parser.hpp>  
#include <boost/locale.hpp>  
#include <fstream>  
#include <sstream>  
#include <iostream>  
  
// Function to convert UTF-16 to UTF-8  
std::string utf16_to_utf8(const std::u16string& utf16_str) {  
    return boost::locale::conv::utf_to_utf<char>(utf16_str);  
}  
  
// Function to read a UTF-16 XML file  
void read_utf16_xml(const std::string& filename, boost::property_tree::ptree& pt) {  
    // Open the file in binary mode  
    std::ifstream file(filename, std::ios::binary);  
    if (!file) {  
        throw std::runtime_error("Failed to open file: " + filename);  
    }  
  
    // Read the entire file into a buffer  
    std::ostringstream buffer;  
    buffer << file.rdbuf();  
    std::string file_content = buffer.str();  
  
    // Convert the file content from UTF-16 to UTF-8  
    std::u16string utf16_str(reinterpret_cast<const char16_t*>(file_content.data()), file_content.size() / sizeof(char16_t));  
    std::string utf8_str = utf16_to_utf8(utf16_str);  
  
    // Parse the UTF-8 XML content  
    std::istringstream utf8_stream(utf8_str);  
    boost::property_tree::read_xml(utf8_stream, pt);  
}  
  
int main() {  
    try {  
        boost::property_tree::ptree pt;  
        std::string filename = "utf16.xml"; // Replace with your UTF-16 XML file  
        read_utf16_xml(filename, pt);  
  
        // Access and print the XML data  
        std::cout << "Root element: " << pt.get<std::string>("root") << std::endl;  
    } catch (const std::exception& e) {  
        std::cerr << "Error: " << e.what() << std::endl;  
    }  
  
    return 0;  
}  
```
