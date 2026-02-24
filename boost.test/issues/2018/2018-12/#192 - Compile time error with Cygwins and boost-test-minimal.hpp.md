# #192 - Compile time error with Cygwins and boost/test/minimal.hpp [Closed]

> Username: apolukhin  
> Created at: 2018-12-16 10:13:28 UTC  
> Updated at: 2019-10-06 19:05:42 UTC  
> Closed at: 2019-10-06 19:05:42 UTC  
> Url: https://github.com/boostorg/test/issues/192  

Attempt to build Boost.Variant tests under Cygwin gives the following error:  
```  
In file included from ..\..\../boost/test/minimal.hpp:54:0,  
                 from rvalue_test.cpp:14:  
..\..\../boost/test/impl/debug.ipp: In function 'const char* boost::debug::{anonymous}::prepare_gdb_cmnd_file(const boost::debug::dbg_startup_info&)':  
..\..\../boost/test/impl/debug.ipp:424:23: error: '::mkstemp' has not been declared  
     fd_holder cmd_fd( ::mkstemp( cmd_file_name ) );  
                       ^~  
In file included from ..\..\../boost/test/minimal.hpp:54:0,  
                 from rvalue_test.cpp:14:  
..\..\../boost/test/impl/debug.ipp: In function 'void boost::debug::debugger_break()':  
..\..\../boost/test/impl/debug.ipp:711:5: error: '::kill' has not been declared  
     ::kill( ::getpid(), SIGTRAP );  
     ^~  
..\..\../boost/test/impl/debug.ipp: In function 'bool boost::debug::attach_debugger(bool)':  
..\..\../boost/test/impl/debug.ipp:892:34: error: '::mkstemp' has not been declared  
     fd_holder init_done_lock_fd( ::mkstemp( init_done_lock_fn ) );  
                                  ^~  
..\..\../boost/test/impl/debug.ipp:929:9: error: '::select' has not been declared  
         ::select( 0, 0, 0, 0, &to );  
         ^~  
```  
  
Whole build logs are available here: https://ci.appveyor.com/project/apolukhin/variant/build/job/q0all7ghv0t975rv

---

## Comment 1

> Username: raffienficiaud  
> Created at: 2018-12-16 10:16:34 UTC  
> Url: https://github.com/boostorg/test/issues/192#issuecomment-447631278  

Boost.test minimal is deprecated since a while. See the header of https://www.boost.org/doc/libs/1_69_0/boost/test/minimal.hpp . Do you have the possibility to switch to the header variant of boost.test?

---

## Comment 2

> Username: apolukhin  
> Created at: 2018-12-17 07:28:53 UTC  
> Url: https://github.com/boostorg/test/issues/192#issuecomment-447747866  

Could you please give a warning if that header is included? Otherwise most of the people just won't notice the deprecation.

---

## Comment 3

> Username: raffienficiaud  
> Created at: 2019-02-03 20:03:37 UTC  
> Url: https://github.com/boostorg/test/issues/192#issuecomment-460083353  

Of course. In the meantime, I do not have Cygwin. Do you have an idea on how to fix your issue?

---

## Comment 4

> Username: raffienficiaud  
> Created at: 2019-10-06 19:05:42 UTC  
> Url: https://github.com/boostorg/test/issues/192#issuecomment-538777625  

I believe this can be closed. Previous versions of boost.test have a warning for all deprecated headers.
