# #97 - XML writer / parser does not implement attribute normalization, it is not conformant [Open]

> Username: bubnikv  
> Created at: 2022-05-04 06:58:47 UTC  
> Updated at: 2025-02-08 23:44:03 UTC  
> Url: https://github.com/boostorg/property_tree/issues/97  

boost::property_tree is able to read the XML attribute values it writes, however it seems to not implement XML attribute normalization:  
https://www.w3.org/TR/REC-xml/#AVNormalize  
  
Namely, if a XML attribute value contains newlines or tabs, these newlines or tabs are replaced by libexpat by spaces after parsing and before passing to an application due to the mandatory normalization as shown by the following table from the XML norm:  
  
![image](https://user-images.githubusercontent.com/5830947/166635232-e25fcdfc-1b48-456e-96ea-c82bc639adb4.png)  
  
Please note that an attribute type is considered CDATA by default.  
  
And by the way, none of the XML parsers / writers that I checked (boost serialization, tinyxml, lib3mf) seem to implement normalization.  
  
This issue is similar to https://github.com/3MFConsortium/lib3mf/issues/288

---

## Comment 1

> Username: ljluestc  
> Created at: 2025-02-08 23:44:02 UTC  
> Url: https://github.com/boostorg/property_tree/issues/97#issuecomment-2645984764  

```  
  
#include <boost/property_tree/ptree.hpp>  
#include <boost/property_tree/xml_parser.hpp>  
#include <boost/algorithm/string.hpp>  
#include <iostream>  
#include <sstream>  
  
// Function to normalize XML attribute values  
std::string normalize_attribute_value(const std::string& value) {  
    std::string normalized_value = value;  
  
    // Replace newlines and tabs with spaces  
    boost::replace_all(normalized_value, "\n", " ");  
    boost::replace_all(normalized_value, "\t", " ");  
  
    // Replace multiple spaces with a single space  
    boost::replace_all(normalized_value, "  ", " ");  
  
    // Trim leading and trailing spaces  
    boost::trim(normalized_value);  
  
    return normalized_value;  
}  
  
// Custom XML writer settings  
struct custom_xml_writer_settings {  
    char indent_char;  
    int indent_count;  
    bool normalize_attributes;  
  
    custom_xml_writer_settings(char indent_char = ' ', int indent_count = 4, bool normalize_attributes = true)  
        : indent_char(indent_char), indent_count(indent_count), normalize_attributes(normalize_attributes) {}  
};  
  
// Custom XML writer  
template <typename Ptree>  
void write_xml_custom(std::basic_ostream<typename Ptree::key_type::value_type>& stream,  
                      const Ptree& pt,  
                      const custom_xml_writer_settings& settings = custom_xml_writer_settings()) {  
    // Create a copy of the property tree to normalize attributes  
    Ptree normalized_pt = pt;  
  
    if (settings.normalize_attributes) {  
        for (auto& node : normalized_pt) {  
            for (auto& attr : node.second) {  
                if (attr.first == "<xmlattr>") {  
                    for (auto& value : attr.second) {  
                        value.second.put_value(normalize_attribute_value(value.second.get_value<std::string>()));  
                    }  
                }  
            }  
        }  
    }  
  
    // Write the normalized property tree to XML  
    boost::property_tree::xml_writer_settings<typename Ptree::key_type::value_type> writer_settings(settings.indent_char, settings.indent_count);  
    boost::property_tree::write_xml(stream, normalized_pt, writer_settings);  
}  
  
int main() {  
    try {  
        // Create a property tree  
        boost::property_tree::ptree pt;  
  
        // Add data with non-normalized attribute values  
        pt.put("root.<xmlattr>.attr1", "Hello\tWorld\n");  
        pt.put("root.<xmlattr>.attr2", "  Multiple   spaces  ");  
        pt.put("root.value", "Some value");  
  
        // Write the property tree to XML with normalization  
        std::ostringstream xml_stream;  
        write_xml_custom(xml_stream, pt);  
  
        // Print the normalized XML  
        std::cout << "Normalized XML:\n" << xml_stream.str() << std::endl;  
  
        // Read the normalized XML back into a property tree  
        boost::property_tree::ptree pt2;  
        std::istringstream xml_input(xml_stream.str());  
        boost::property_tree::read_xml(xml_input, pt2);  
  
        // Print the attribute values to verify normalization  
        std::cout << "attr1: " << pt2.get<std::string>("root.<xmlattr>.attr1") << std::endl;  
        std::cout << "attr2: " << pt2.get<std::string>("root.<xmlattr>.attr2") << std::endl;  
    } catch (const std::exception& e) {  
        std::cerr << "Error: " << e.what() << std::endl;  
    }  
  
    return 0;  
}  
```
