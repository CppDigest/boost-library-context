# #755 - Redundant declaration errors from Boost Assert compiling with g++ -Werror -Wredundant-decls -DBOOST_ENABLE_ASSERT_HANDLER [Open]

> Username: jleffler  
> Created at: 2023-03-20 20:32:10 UTC  
> Updated at: 2023-03-20 20:32:10 UTC  
> Url: https://github.com/boostorg/boost/issues/755  

Consider the (minimal) source file `redundant.cpp` (yes, just two `#include` lines)  
```  
#include "boost/shared_ptr.hpp"  
#include "boost/shared_array.hpp"  
```  
Consider the shell script that compiles that — where there is a copy of Boost 1.81.0 in the sub-directory `boost_1_81_0` of the current directory:  
```  
#!/bin/sh  
  
source=redundant.cpp  
: "${boostdir:=./boost_1_81_0}"  
  
set -x  
g++ -c -Werror -Wredundant-decls -DBOOST_ENABLE_ASSERT_HANDLER \  
    -I"$boostdir" "$source"  
```  
Because of the two `-W` and one `-D` options, the code fails to compile, with errors:  
```  
+ g++ -c -Werror -Wredundant-decls -DBOOST_ENABLE_ASSERT_HANDLER -I./boost_1_81_0 redundant.cpp  
In file included from ./boost_1_81_0/boost/smart_ptr/shared_array.hpp:21,  
                 from ./boost_1_81_0/boost/shared_array.hpp:17,  
                 from redundant.cpp:2:  
./boost_1_81_0/boost/assert.hpp:49:10: error: redundant redeclaration of ‘void boost::assertion_failed(const char*, const char*, const char*, long int)’ in same scope [-Werror=redundant-decls]  
   49 |     void assertion_failed(char const * expr, char const * function, char const * file, long line); // user defined  
      |          ^~~~~~~~~~~~~~~~  
In file included from ./boost_1_81_0/boost/smart_ptr/shared_ptr.hpp:24,  
                 from ./boost_1_81_0/boost/shared_ptr.hpp:17,  
                 from redundant.cpp:1:  
./boost_1_81_0/boost/assert.hpp:49:10: note: previous declaration of ‘void boost::assertion_failed(const char*, const char*, const char*, long int)’  
   49 |     void assertion_failed(char const * expr, char const * function, char const * file, long line); // user defined  
      |          ^~~~~~~~~~~~~~~~  
In file included from ./boost_1_81_0/boost/smart_ptr/shared_array.hpp:21,  
                 from ./boost_1_81_0/boost/shared_array.hpp:17,  
                 from redundant.cpp:2:  
./boost_1_81_0/boost/assert.hpp:50:10: error: redundant redeclaration of ‘void boost::assertion_failed_msg(const char*, const char*, const char*, const char*, long int)’ in same scope [-Werror=redundant-decls]  
   50 |     void assertion_failed_msg(char const * expr, char const * msg, char const * function, char const * file, long line); // user defined  
      |          ^~~~~~~~~~~~~~~~~~~~  
In file included from ./boost_1_81_0/boost/smart_ptr/shared_ptr.hpp:24,  
                 from ./boost_1_81_0/boost/shared_ptr.hpp:17,  
                 from redundant.cpp:1:  
./boost_1_81_0/boost/assert.hpp:50:10: note: previous declaration of ‘void boost::assertion_failed_msg(const char*, const char*, const char*, const char*, long int)’  
   50 |     void assertion_failed_msg(char const * expr, char const * msg, char const * function, char const * file, long line); // user defined  
      |          ^~~~~~~~~~~~~~~~~~~~  
cc1plus: all warnings being treated as errors  
```  
The file (`boost_1_81_0/boost/assert.hpp`) can be fixed easily by adding a guard around the declaration of the two functions:  
```  
$ diff -u boost_1_81_0/boost/assert.hpp boost_1_81_0/boost/assert-20230317.142448.hpp   
--- boost_1_81_0/boost/assert.hpp	2023-03-17 15:12:31.136740000 -0600  
+++ boost_1_81_0/boost/assert-20230317.142448.hpp	2023-03-17 14:24:48.699552000 -0600  
@@ -44,11 +44,15 @@  
 #include <boost/config.hpp> // for BOOST_LIKELY  
 #include <boost/current_function.hpp>  
   
+// Avoid warnings from -Wredundant-decls  
+#ifndef BOOST_ASSERT_DECLARE_ASSERTION_FAILED_FUNCTIONS  
+#define BOOST_ASSERT_DECLARE_ASSERTION_FAILED_FUNCTIONS  
 namespace boost  
 {  
     void assertion_failed(char const * expr, char const * function, char const * file, long line); // user defined  
     void assertion_failed_msg(char const * expr, char const * msg, char const * function, char const * file, long line); // user defined  
 } // namespace boost  
+#endif  
   
 #define BOOST_ASSERT(expr) (BOOST_LIKELY(!!(expr))? ((void)0): ::boost::assertion_failed(#expr, BOOST_CURRENT_FUNCTION, __FILE__, __LINE__))  
 #define BOOST_ASSERT_MSG(expr, msg) (BOOST_LIKELY(!!(expr))? ((void)0): ::boost::assertion_failed_msg(#expr, msg, BOOST_CURRENT_FUNCTION, __FILE__, __LINE__))  
$  
```  
I don't know if the comment would be retained in the final version of the code.  And I've chosen what seemed like a plausible macro name for protecting the code, but I'm perfectly happy with any equivalent choice.  With this fix in place, the file `redundant.cpp` compiles without errors.  Of course, it does nothing useful, but that's what happens when you minimize the code that reproduces the problem.
