# #40 - property_tree/xml_parser is conceptually flawed, because it can't parse XMLs with the mix of texts and child trees [Open]

> Username: yurivict  
> Created at: 2019-06-13 03:33:53 UTC  
> Updated at: 2019-06-13 03:33:53 UTC  
> Url: https://github.com/boostorg/property_tree/issues/40  

This XML:  
```  
<?xml version="1.0" encoding="UTF-8"?>  
<tag>  
  Text1  
  <tag1>a</tag1>  
  Text2  
  <tag2>b</tag2>  
  Text3  
</tag>  
```  
parse result has all texts (Text1, Text2, Text3) combined into a single string that is available as the output of the ```data()``` function:  
```  
data="  
  Text1  
    
  Text2  
    
  Text3  
"  
```  
and boost leaves no way to determine that ```<tag1>``` and ```<tag2>``` are in the middle of this text. ```tag1``` and ```tag2``` are available as separate keys, but this loses information about their location.  
  
A larger practical example:  
The line ```<param len="4">const <ptype>GLfloat</ptype> *<name>v</name></param>``` in https://raw.githubusercontent.com/KhronosGroup/OpenGL-Registry/master/xml/gl.xml can't be parsed using ```property_tree/xml_parser``` because the ```<ptype>``` tag is between two text elements.
