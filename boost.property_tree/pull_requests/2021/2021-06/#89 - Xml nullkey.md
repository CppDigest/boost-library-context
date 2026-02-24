# #89 Xml nullkey [Closed]

> Username: brchrisman  
> Created at: 2021-06-15 17:54:30 UTC  
> Updated at: 2021-06-18 19:18:37 UTC  
> Closed at: 2021-06-18 19:18:37 UTC  
> Url: https://github.com/boostorg/property_tree/pull/89  

Added test and fixed string literal compatibility.

---

## Comment 1

> Username: madmongo1  
> Created_at: 2021-06-15 19:00:08 UTC  
> Url: https://github.com/boostorg/property_tree/pull/89#issuecomment-861755898  

Many thanks for the commit.  
  
Is this seeking to change existing behaviour in legacy applications?

---

## Comment 2

> Username: brchrisman  
> Created_at: 2021-06-15 19:19:30 UTC  
> Url: https://github.com/boostorg/property_tree/pull/89#issuecomment-861767865  

> Many thanks for the commit.  
>   
> Is this seeking to change existing behaviour in legacy applications?  
  
No, it's fixing an apparently otherwise unused case where xml serialization of a property-tree with a null key will output null qnames/tagnames `<>` and `</>` which aren't well-formed XML.  
I ran across this while serializing a property-tree to xml, then patched it locally in my particular .hpp copy and figured I'd push a change upstream.  
It's nearly impossible that any applications are counting on property-tree-xml-serialization producing broken elements.

---

## Comment 3

> Username: brchrisman  
> Created_at: 2021-06-15 19:34:45 UTC  
> Url: https://github.com/boostorg/property_tree/pull/89#issuecomment-861776992  

It looks like   
```  
./boost/property_tree/detail/xml_parser_write.hpp:113:17: error: no match for ternary ‘operator?:’ in ‘(& key)->std::basic_string<_CharT, _Traits, _Alloc>::empty<wchar_t, std::char_traits<wchar_t>, std::allocator<wchar_t> >() ? nullkey : key’  
./boost/property_tree/detail/xml_parser_write.hpp:171:17: error: no match for ternary ‘operator?:’ in ‘(& key)->std::basic_string<_CharT, _Traits, _Alloc>::empty<wchar_t, std::char_traits<wchar_t>, std::allocator<wchar_t> >() ? nullkey : key’  
```  
looks like my char * nullkey needs to be the same type as 'key'... not sure how this compiles ok on my particular application... will get them to be of the same type.

---

## Comment 4

> Username: madmongo1  
> Created_at: 2021-06-15 20:06:49 UTC  
> Url: https://github.com/boostorg/property_tree/pull/89#issuecomment-861796446  

Yep, CI confirms the compilation failure.

---
