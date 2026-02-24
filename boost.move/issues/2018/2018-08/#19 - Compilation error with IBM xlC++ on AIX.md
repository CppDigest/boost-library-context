# #19 - Compilation error with IBM xlC++ on AIX [Closed]

> Username: chrahunt  
> Created at: 2018-08-31 23:03:33 UTC  
> Updated at: 2018-09-08 22:17:32 UTC  
> Closed at: 2018-09-08 22:17:32 UTC  
> Url: https://github.com/boostorg/move/issues/19  

Boost version: 1.68.0  
Platform: AIX 7.1 TL04 SP01  
Compiler: xlC++ v13.1.3  
  
```  
$ xlC -qversion  
IBM XL C/C++ for AIX, V13.1.3 (5725-C72, 5765-J07)  
Version: 13.01.0003.0000  
$ cat test.cpp  
#include <boost/move/core.hpp>  
int main() {}  
$ xlC -I/src/boost_1_68_0 test.cpp  
"/src/boost_1_68_0/boost/move/core.hpp", line 84.4: 1540-0064 (S) Syntax error:  "base specifier" was expected but "}" was found.  
```  
  
It looks like `__attribute__((__may_alias__))` is only stated as being supported before the type name per [here](https://www.ibm.com/support/knowledgecenter/SSGH2K_13.1.2/com.ibm.xlc131.aix.doc/language_ref/type_attr_may_alias.html) (in practice this only seems to be necessary on template types). Having the alias before the type in declarations is also used throughout the rest of Boost: every usage of MAY_ALIAS I could find as part of a class declaration specifies the attribute  after the class/struct keyword and before the type name. e.g.  
  
* [`atomic/detail/storage_type.hpp`](https://github.com/boostorg/atomic/blob/640f1796b0fdb8bbd47dbae7054b76f60b783dde/include/boost/atomic/detail/storage_type.hpp)  
* [`log/detail/threadsafe_queue.hpp`](https://github.com/boostorg/log/blob/d3f3776024d91dc48115db7a59aa6764e9b75c05/include/boost/log/detail/threadsafe_queue.hpp)
