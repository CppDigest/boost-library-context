# #18 Ticket 11600 - Part 2, Change of Whitespace encoding in Text nodes [Merged]

> Username: timostrunk  
> Created at: 2016-02-10 15:59:19 UTC  
> Updated at: 2016-02-11 09:40:48 UTC  
> Merged at: 2016-02-11 09:40:48 UTC  
> Closed at: 2016-02-11 09:40:48 UTC  
> Url: https://github.com/boostorg/property_tree/pull/18  

This pull request removes the encoding of \t and \n in Text nodes of XML documents.  
  
It's mentioned as part of the bug report (under second bug at the bottom):  
https://svn.boost.org/trac/boost/ticket/11600  
The testcase attached to the bug report can still be used to test this pull request.  
If merged, stage3.xml is identical to in.xml.  
  
Previously the following xml tag:  
 R is non-whitespace letter  
  
```  
<Mytext> RRRRRRRRRRRRR  
RRRR RRRRRR</Mytext>  
```  
  
would be encoded to:  
  
```  
<Mytext> RRRRRRRRRRRRR &#10;RRRR RRRRRR</Mytext>  
```  
  
on roundtrip.  
The same thing happens with  
  
```  
\t --> &#9;  
```  
  
This is unnecessary, because \n and \t aren't characters interpreted as control characters and therefore don't need protection  (in my opinion, but I'm not as sure about this as I was in my last pull request). It changed recently sometime between boost 1.55 and 1.59.  
  
Last remark: if one parses the XML using:  
xml_parser::read_xml(ptree_stream,output_tree, boost::property_tree::xml_parser::trim_whitespace )  
the internal whitespace is collapsed. This is to be expected and correct, if one reviews the code:  
     \* @li @c trim_whitespace -- Trim leading and trailing whitespace from text,  
     \*                           and collapse sequences of whitespace.

---
