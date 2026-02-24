# #31 Docs build: flags --maxdepth 100000 [Merged]

> Username: sdarwin  
> Created at: 2023-09-15 00:43:58 UTC  
> Updated at: 2025-05-31 18:41:01 UTC  
> Merged at: 2025-05-31 18:41:01 UTC  
> Closed at: 2025-05-31 18:41:01 UTC  
> Url: https://github.com/boostorg/contract/pull/31  

The boostorg/contract documentation is able to build in Linux, however not on MacOS or Windows.   Not clear why...     
  
If a maintainer is interested I could convert this into an Issue, to keep track of it.    
  
The issue is "be able to build documentation on MacOS and Microsoft Windows".   Which may be low priority.  It's somewhat interesting to be able to use boost libraries on multiple operating systems.  Scripts to facilitate those builds: https://github.com/boostorg/release-tools/tree/master/build_docs  
  
All docs builds, including Linux, show this error:  
  
```  
warning: tag INCLUDE_PATH: include path '../include' does not exist  
warning: source '../include' is not a readable file or directory... skipping.  
```  
  
A snippet from MacOS:  
  
```  
Cannot find class named 'Class'  
Cannot find class named 'Class'  
Cannot find class named 'const'  
Cannot find class named 'const'  
Cannot find class named 'bool'  
Cannot find class named 'bool'  
Cannot find class named 'bool'  
Cannot find class named 'bool'  
Cannot find class named 'bool'  
Cannot find class named 'bool'  
Cannot find class named 'ifdef'  
Cannot find class named 'BOOST_CONTRACT_DETAIL_DOXYGEN'  
Cannot find class named 'v'  
  
    XML_CATALOG_FILES="bin.v2/boostbook_catalog.xml"  
export XML_CATALOG_FILES  
 "xsltproc" --stringparam boost.defaults "Boost" --stringparam boost.root "../../../.." --stringparam toc.max.depth "1" --path "bin.v2" --path "libs/contract/doc/html" --xinclude -o "libs/contract/doc/html/contract.docbook" "/Users/runner/work/release-tools/release-tools/github/boostorg/boost-root/tools/boostbook/xsl/docbook.xsl" "bin.v2/libs/contract/doc/contract.xml"  
  
...failed xslt-xsltproc libs/contract/doc/html/contract.docbook...  
...skipped <plibs/contract/doc/html>boostrelease_HTML.manifest for lack of <plibs/contract/doc/html>contract.docbook...  
...failed updating 1 target...  
...skipped 1 target...  
...updated 10 targets...  
Error: Process completed with exit code 1.  
```  
  
Errors that appeared on MacOS:  
  
```  
You can adjust xsltMaxDepth (--maxdepth) in order to raise the maximum number of nested template calls and variables/params (currently set to 3000).  
  
You can adjust maxTemplateVars (--maxvars) in order to raise the maximum number of variables/params (currently set to 15000).  
```  
  
Those are fixed in this pull request, by increasing the limits.  Should be safe to merge.  
  
```  
<flags>"--maxdepth 100000"  
<flags>"--maxvars 300000"  
```  
  
That solves those particular errors, but still not everything.

---
