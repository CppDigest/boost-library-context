# #119 - Missing files breaks develop documentation build [Closed]

> Username: glenfe  
> Created at: 2019-05-14 13:09:29 UTC  
> Updated at: 2019-05-17 06:14:53 UTC  
> Closed at: 2019-05-17 06:14:37 UTC  
> Url: https://github.com/boostorg/container/issues/119  

The following two commits have broken the build of all develop documentation at https://www.boost.org/doc/libs/develop/:  
* 4c348af1eb1290f9f20f277f76266df16f91b5ea  
* 5d96b11fe0d8351f2a9ee3960f5c2da7ba9b6642  
  
They reference the following files which don't exist:  
* example/doc_custom_static_vector.cpp  
* example/doc_custom_small_vector.cpp  
  
> container.qbk:744: error: Unable to find file: ../example/doc_custom_static_vector.cpp  
> container.qbk:771: error: Unable to find file: ../example/doc_custom_small_vector.cpp  
> Error: Error count: 2.

---

## Comment 1

> Username: pdimov  
> Created at: 2019-05-17 00:36:42 UTC  
> Url: https://github.com/boostorg/container/issues/119#issuecomment-493277171  

Please fix.

---

## Comment 2

> Username: igaztanaga  
> Created at: 2019-05-17 06:14:53 UTC  
> Url: https://github.com/boostorg/container/issues/119#issuecomment-493334200  

Sorry and thanks for the report.
