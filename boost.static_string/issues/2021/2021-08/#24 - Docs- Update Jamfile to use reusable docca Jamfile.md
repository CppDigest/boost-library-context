# #24 - Docs: Update Jamfile to use reusable docca Jamfile [Closed]

> Username: evanlenz  
> Created at: 2021-08-05 06:27:47 UTC  
> Updated at: 2022-11-01 21:11:33 UTC  
> Closed at: 2022-11-01 21:11:33 UTC  
> Url: https://github.com/boostorg/static_string/issues/24  

Use the pattern shown here:  
https://github.com/boostorg/beast/blob/develop/doc/Jamfile  
  
And here:  
https://github.com/boostorg/json/blob/develop/doc/Jamfile

---

## Comment 1

> Username: alandefreitas  
> Created at: 2022-11-01 20:49:34 UTC  
> Url: https://github.com/boostorg/static_string/issues/24#issuecomment-1299126818  

The script is failing with this error:  
  
<details>  
  
  
console  
```  
Generate XML output for dir /home/alandefreitas/Documents/Code/C++/boost/libs/static_string/include/boost/static_string/  
lookup cache used 488/65536 hits=22543 misses=498  
finished...  
saxonhe.saxonhe bin.v2/libs/static_string/doc/_reference-dir/xml-pages.xml  
Warning at char 9 in xsl:sequence/@select on line 78 column 84 of base-extract-xml-pages.xsl:  
  XTDE1160: The fragment identifier {ifdef_01BOOST__STATIC__STRING_...} is not a valid NCName  
Error evaluating (fn:document(...)) in xsl:sequence/@select on line 78 column 84 of base-extract-xml-pages.xsl:  
  FODC0002: I/O error reported by XML parser processing  
  file:/home/alandefreitas/Documents/Code/C++/boost/bin.v2/libs/static_string/doc/_reference-dir/structstd_1_1hash_3: /home/alandefreitas/Documents/Code/C++/boost/bin.v2/libs/static_string/doc/_reference-dir/structstd_1_1hash_3 (No such file or directory)  
at function d:get-source-doc on line 76 of base-extract-xml-pages.xsl:  
     invoked by function call at file:/home/alandefreitas/Documents/Code/C++/boost/bin.v2/libs/static_string/doc/_reference-dir/base-extract-xml-pages.xsl#53  
  In template rule with match="compound" on line 51 of base-extract-xml-pages.xsl  
     invoked by xsl:apply-templates at file:/home/alandefreitas/Documents/Code/C++/boost/bin.v2/libs/static_string/doc/_reference-dir/base-extract-xml-pages.xsl#26  
  In template rule with match="/" on line 24 of base-extract-xml-pages.xsl  
I/O error reported by XML parser processing file:/home/alandefreitas/Documents/Code/C++/boost/bin.v2/libs/static_string/doc/_reference-dir/structstd_1_1hash_3: /home/alandefreitas/Documents/Code/C++/boost/bin.v2/libs/static_string/doc/_reference-dir/structstd_1_1hash_3 (No such file or directory)  
  
    "java" -jar "/usr/share/java//Saxon-HE.jar" -o:"bin.v2/libs/static_string/doc/_reference-dir/xml-pages.xml" -s:"bin.v2/libs/static_string/doc/_reference-dir/index.xml" -xsl:"bin.v2/libs/static_string/doc/_reference-dir/extract-xml-pages.xsl"  
  
...failed saxonhe.saxonhe bin.v2/libs/static_string/doc/_reference-dir/xml-pages.xml...  
...removing bin.v2/libs/static_string/doc/_reference-dir/xml-pages.xml  
...skipped <pbin.v2/libs/static_string/doc>_reference-dir/stage1/results for lack of <pbin.v2/libs/static_string/doc>_reference-dir/xml-pages...  
...skipped <pbin.v2/libs/static_string/doc>_reference-dir/stage2/results for lack of <pbin.v2/libs/static_string/doc>_reference-dir/stage1/results...  
...skipped <pbin.v2/libs/static_string/doc>reference.qbk for lack of <pbin.v2/libs/static_string/doc-object(xinclude-scanner)@6545>_reference-dir/xml-pages.xml...  
...skipped <pbin.v2/libs/static_string/doc>static_string_doc.xml for lack of <pbin.v2/libs/static_string/doc-object(qbk-scanner)@6551>reference.qbk...  
...skipped <pbin.v2/libs/static_string/doc>static_string_doc.docbook for lack of <pbin.v2/libs/static_string/doc-object(xinclude-scanner)@6550>static_string_doc.xml...  
...skipped <plibs/static_string/doc/html>static_string_HTML.manifest for lack of <pbin.v2/libs/static_string/doc>static_string_doc.docbook...  
...failed updating 1 target...  
...skipped 6 targets...  
...updated 2 targets...  
```  
  
</details>  
  
The error happens before and after updating Jamfile as recommended here.  
  
Any ideas?
