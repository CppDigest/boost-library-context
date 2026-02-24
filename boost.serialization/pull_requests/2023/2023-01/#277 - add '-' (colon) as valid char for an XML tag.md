# #277 add ':' (colon) as valid char for an XML tag [Merged]

> Username: TobiasLange75443  
> Created at: 2023-01-12 09:29:16 UTC  
> Updated at: 2023-08-23 18:50:09 UTC  
> Merged at: 2023-08-23 18:50:09 UTC  
> Closed at: 2023-08-23 18:50:09 UTC  
> Url: https://github.com/boostorg/serialization/pull/277  

According to the description https://www.boost.org/doc/libs/1_81_0/libs/serialization/doc/exceptions.html#xml_archive_tag_name_error -> xml_archive_tag_name_error  
':' (colon) is a valid character for XML tag names. But if a colon is used in a tag name, an xml_archive_tag_name_error - exception is thrown. The code change defines ':' colon as valid character.  
  
Examplecode:  
  
```  
#include <iostream>  
#include <sstream>  
#include <string>  
#include <boost/archive/xml_oarchive.hpp>  
#include <boost/archive/xml_iarchive.hpp>  
#include <boost/archive/xml_archive_exception.hpp>  
#include <boost/archive/impl/basic_xml_oarchive.ipp>  
  
int main()  
{  
  try  
  {  
    boost::archive::detail::XML_name<const char>{}(':');  
  }  
  catch (boost::archive::xml_archive_exception& e)  
  {  
    std::cout << e.what();  
  }  
  
  const auto tag{ "A:B" };  
  std::ostringstream oss;  
  {  
    try  
    {  
      boost::archive::xml_oarchive oa{ oss };  
      int value{ 25 };  
      oa << boost::serialization::make_nvp(tag, value);  
    }  
    catch (boost::archive::xml_archive_exception& e)  
    {  
      std::cout << e.what();  
    }  
  }  
  std::cout << oss.str();  
  {  
    std::istringstream iss{ oss.str() };  
    boost::archive::xml_iarchive ia{ iss };  
    int value{ 0 };  
    ia >> boost::serialization::make_nvp(tag, value);  
  }  
  
  return 0;  
}  
```

---
