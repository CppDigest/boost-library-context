# #46 - Ability to suppress xml version/encoding processing instruction [Closed]

> Username: leppardb  
> Created at: 2019-12-15 14:30:42 UTC  
> Updated at: 2023-11-15 09:57:15 UTC  
> Closed at: 2023-11-15 09:57:15 UTC  
> Url: https://github.com/boostorg/property_tree/issues/46  

My use case is to inject xml snippets into comment blocks as templates for users to modify and un-comment as desired. Because the comment marker tag only accepts a comment string (subnodes not supported), I generate the xml snippets separately and  write them to a stringstream then inject the string into the xml comment tag. Each time a snippet xml is generated the \<?xml version="1.0" encoding="utf-8"?\> processing instruction is automatically inserted.  
  
I want to be able to suppress emitting this instruction. I suggest that if the encoding attribute in xml_writer_settings is explicitly set to an "empty" string then automatic insertion of the version/encoding instruction be skipped.

---

## Comment 1

> Username: TomFD  
> Created at: 2022-02-10 19:34:45 UTC  
> Url: https://github.com/boostorg/property_tree/issues/46#issuecomment-1035398689  

looking at the implementation, you can skip the instruction and only use the internal function xml_write_element. Here is a full example:  
```  
#include <boost/property_tree/xml_parser.hpp>  
#include <iostream>  
  
int main() {  
typedef typename boost::property_tree::ptree::key_type Str;  
  
    boost::property_tree::ptree tree;  
    tree.put("test", "somevalue");  
  
    boost::property_tree::xml_parser::write_xml_element(  
        std::cout, Str(),tree, -1,boost::property_tree::xml_writer_settings<std::string>(' ', 4));  
}  
```  
to get the output  
```  
<test>somevalue</test>  
```
