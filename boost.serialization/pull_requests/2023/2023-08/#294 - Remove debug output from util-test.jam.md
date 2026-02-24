# #294 Remove debug output from util/test.jam [Merged]

> Username: pdimov  
> Created at: 2023-08-25 21:30:14 UTC  
> Updated at: 2023-08-27 15:55:41 UTC  
> Merged at: 2023-08-27 15:55:41 UTC  
> Closed at: 2023-08-27 15:55:41 UTC  
> Url: https://github.com/boostorg/serialization/pull/294  

This comments out an `ECHO` statement in `util/test.jam`, which was probably used for debugging purposes. It results in the output  
```  
polymorphic_text_archive  
polymorphic_text_warchive  
polymorphic_binary_archive  
polymorphic_xml_archive  
polymorphic_xml_warchive  
polymorphic_text_archive  
polymorphic_text_warchive  
polymorphic_binary_archive  
polymorphic_xml_archive  
polymorphic_xml_warchive  
polymorphic_text_archive  
polymorphic_text_warchive  
polymorphic_binary_archive  
polymorphic_xml_archive  
polymorphic_xml_warchive  
polymorphic_text_archive  
polymorphic_text_warchive  
polymorphic_binary_archive  
polymorphic_xml_archive  
polymorphic_xml_warchive  
polymorphic_text_archive  
polymorphic_text_warchive  
polymorphic_binary_archive  
polymorphic_xml_archive  
polymorphic_xml_warchive  
polymorphic_text_archive  
polymorphic_text_warchive  
polymorphic_binary_archive  
polymorphic_xml_archive  
polymorphic_xml_warchive  
```  
before  
```  
Performing configuration checks  
  
    - default address-model    : 64-bit (cached) [1]  
    - default architecture     : x86 (cached) [1]  
    - symlinks supported       : yes (cached)  
```  
on each `b2` invocation, which is unnecessary.

---
