# #76 - Quickbook warning [Open]

> Username: grisumbras  
> Created at: 2021-09-08 16:58:22 UTC  
> Updated at: 2021-09-08 16:58:22 UTC  
> Url: https://github.com/boostorg/docca/issues/76  

From build output:  
```  
Generating output file: ../../bin.v2/libs/json/doc/json_doc.xml  
/home/grisumbras/dev/cppal/boost/bin.v2/libs/json/doc/reference.qbk|92| warning: line breaks generate invalid boostbook (will only note first occurrence).  
```  
  
reference.qbk:92  
```  
    [Constructor. [br][role silver —][br]Copy constructor. [br][role silver —][br]Pilfer constructor. [br][role silver —][br]Move constructor.   
```  
  
I've looked through quickbook sources, the tool seems to warn on _any_ use of `[br]`, so the only way to remove the warning is probably to use paragraphs.
