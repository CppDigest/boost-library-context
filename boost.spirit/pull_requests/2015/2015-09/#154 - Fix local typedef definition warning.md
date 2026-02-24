# #154 Fix local typedef definition warning [Merged]

> Username: stac47  
> Created at: 2015-09-07 11:54:15 UTC  
> Updated at: 2015-09-07 11:57:16 UTC  
> Merged at: 2015-09-07 11:57:16 UTC  
> Closed at: 2015-09-07 11:57:16 UTC  
> Url: https://github.com/boostorg/spirit/pull/154  

Hello,  
I submit this tiny cleanup PR since this causes the following warning (treated as error)  
  
```  
...  
include/boost/spirit/home/karma/string/lit.hpp: In member function 'bool boost::spirit::karma::literal_string<String, CharEncoding, Tag, no_attribute>::gen  
erate(OutputIterator&, Context&, const Delimiter&, const Attribute&) const':  
include/boost/spirit/home/karma/string/lit.hpp:179:17: error: typedef 'extracted_string_type' locally defined but not used [-Werror=unused-local-typedefs]  
                 extracted_string_type;  
                 ^  
cc1plus: all warnings being treated as errors  
```  
  
The definition of _extracted_string_type_  was introduced by commit 555ce82 and its use removed some days later through 2d4f293. So I guess this local definition can be safely removed.  
To you for validation.  
## Regards,  
  
Platform: linux  
Compiler: gcc 4.9.3

---
