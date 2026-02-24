# #350 - print_escaped_cdata only escapes first cdata [Open]

> Username: davidmatson  
> Created at: 2022-08-08 23:00:22 UTC  
> Updated at: 2022-08-08 23:00:43 UTC  
> Url: https://github.com/boostorg/test/issues/350  

This works:  
```c++  
BOOST_AUTO_TEST_CASE(one_embedded_cdata_section)  
{  
    BOOST_REQUIRE_MESSAGE(false, "<![CDATA[EmbeddedCDATA]]>AdditionalTextAfterwards");  
}  
```  
  
This produces invalid XML data using -o XML:  
```c++  
BOOST_AUTO_TEST_CASE(two_embedded_cdata_sections)  
{  
    BOOST_REQUIRE_MESSAGE(false, "<![CDATA[EmbeddedCDATA1]]><![CDATA[EmbeddedCDATA2]]>AdditionalTextAfterwards");  
}  
```  
  
I believe the if test here:  
[    if( pos == const_string::npos )  
](https://github.com/boostorg/test/blob/9d863d07e864ef663e3e8573b55905099b938d3e/include/boost/test/utils/xml_printer.hpp#L102)  
needs to be a loop instead.
