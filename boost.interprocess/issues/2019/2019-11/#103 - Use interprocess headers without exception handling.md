# #103 - Use interprocess headers without exception handling [Closed]

> Username: cngzhnp  
> Created at: 2019-11-18 05:32:22 UTC  
> Updated at: 2021-08-23 11:38:58 UTC  
> Closed at: 2021-08-23 11:32:13 UTC  
> Url: https://github.com/boostorg/interprocess/issues/103  

Boost Version : 1.71  
  
By default when try to add interprocess header files, **exceptions are enabled** by default and it cannot be disabled with any preprocessor or configuration.  
  
In Boost, there is a preprocessor defined `BOOST_NO_EXCEPTIONS` which used in many other places. If user does not want to implement exception handling in their project and do not want to add flag like `-fexceptions` , it has to be configurable in one of the config files.   
  
So, before adding `<boost/interprocess/exceptions.hpp>` header file in the interprocess component, it has to be checked before `BOOST_NO_EXCEPTIONS` was defined in config files.  
  
I looked deeply a little bit more on implementation and there is an inconsistency between exception handling in interprocess files. In some of them, problem was solved by adding `no_exceptions_support.hpp`, please take a look on `segment_manager.hpp`   
  
However, the other files has to be considered as well and common solution has to be used.   
  
My proposal is, with checking `BOOST_NO_EXCEPTIONS` compiler has to choose which file is included (in this case `<boost/interprocess/exceptions.hpp>` and `#include <boost/core/no_exceptions_support.hpp>`).   
  
Also default keywords ( `try`, `catch`, `throw` ) has to be replaced with corresponding preprocessor definitions such as ( `BOOST_TRY`, `BOOST_CATCH`, `BOOST_RETHROW` )

---

## Comment 1

> Username: raspopov  
> Created at: 2020-12-23 15:55:04 UTC  
> Url: https://github.com/boostorg/interprocess/issues/103#issuecomment-750361668  

Boost Version: 1.75  
Compiled for embedded QNX 6.5 (GCC 4.3) without exceptions.  
  
This issue requires just a straight and mechanical replacement of ```try```, ```catch```, ```throw``` to ```BOOST_TRY```, ```BOOST_CATCH```/```BOOST_CATCH_END```, ```BOOST_RETHROW```/```BOOST_THROW_EXCEPTION``` as mentioned author above and including a ```#include <boost/exception/all.hpp>``` (which is a good practice anyway) and ```#include <boost/core/no_exceptions_support.hpp>``` to the top of ```<boost/interprocess/exceptions.hpp>```.  
  
During adapting and compiling to QNX I done it but only for subset of used headers only, seems all works well.

---

## Comment 2

> Username: igaztanaga  
> Created at: 2021-08-23 11:38:58 UTC  
> Url: https://github.com/boostorg/interprocess/issues/103#issuecomment-903687392  

Thanks for the report. As the request does not require a design change (just be able to include interprocess with no exceptions support, with no additional modifications to support alternative error mechanisms when exceptions are off) I've modified all headers to use <boost/core/no_exceptions_support.hpp>.
