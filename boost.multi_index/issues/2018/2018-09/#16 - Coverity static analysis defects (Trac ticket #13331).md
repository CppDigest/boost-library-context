# #16 - Coverity static analysis defects (Trac ticket #13331) [Closed]

> Username: mloskot  
> Created at: 2018-09-21 08:55:11 UTC  
> Updated at: 2018-09-23 08:27:20 UTC  
> Closed at: 2018-09-23 08:27:20 UTC  
> Url: https://github.com/boostorg/multi_index/issues/16  

(copied from https://svn.boost.org/trac10/ticket/13331, only MultiIndex's parts from the attached report)  
  
Boost 1.57.0  
  
A static analysis tool called Coverity found medium and high defects in the boost source code. See attached file for defect type, defect category, filename and line number of defect.  
  
Defect   Type | Defect Category | Line Number | Filename  
-- | -- | -- | --  
Uninitialized   pointer field | Uninitialized members | 990 | /boost/include/boost/multi_index/ordered_index.hpp

---

## Comment 1

> Username: joaquintides  
> Created at: 2018-09-23 08:27:20 UTC  
> Url: https://github.com/boostorg/multi_index/issues/16#issuecomment-423800325  

This was fixed some years ago at 08fe6a360acf99e1732a54ef8b3114455f59116b.
