# #144 - Building something on cygwin (32 and 64-bit) that uses Boost.Test requires cxxstd-dialect=gnu [Closed]

> Username: jeking3  
> Created at: 2018-05-19 00:06:17 UTC  
> Updated at: 2018-05-20 01:56:28 UTC  
> Closed at: 2018-05-20 01:56:28 UTC  
> Url: https://github.com/boostorg/test/issues/144  

I've been adding CI environments to the CMT projects and there's a common problem I have found with cygwin targets on appveyor, they show up like this:  
```  
gcc.compile.c++ bin.v2\libs\property_map\test\compose_property_map_test.test\gcc-6.4.0\release\cxxstd-03-iso\target-os-cygwin\compose_property_map_test.o  
In file included from ./boost/test/minimal.hpp:54:0,  
                 from libs\property_map\test\compose_property_map_test.cpp:11:  
./boost/test/impl/debug.ipp: In function 'const char* boost::debug::{anonymous}::prepare_gdb_cmnd_file(const boost::debug::dbg_startup_info&)':  
./boost/test/impl/debug.ipp:424:23: error: '::mkstemp' has not been declared  
     fd_holder cmd_fd( ::mkstemp( cmd_file_name ) );  
                       ^~  
In file included from ./boost/test/minimal.hpp:54:0,  
                 from libs\property_map\test\compose_property_map_test.cpp:11:  
./boost/test/impl/debug.ipp: In function 'void boost::debug::debugger_break()':  
./boost/test/impl/debug.ipp:711:5: error: '::kill' has not been declared  
     ::kill( ::getpid(), SIGTRAP );  
     ^~  
./boost/test/impl/debug.ipp: In function 'bool boost::debug::attach_debugger(bool)':  
./boost/test/impl/debug.ipp:892:34: error: '::mkstemp' has not been declared  
     fd_holder init_done_lock_fd( ::mkstemp( init_done_lock_fn ) );  
                                  ^~  
./boost/test/impl/debug.ipp:929:9: error: '::select' has not been declared  
         ::select( 0, 0, 0, 0, &to );  
         ^~  
```  
  
It looks like missing includes, perhaps.  
Here's a build job showing it.  
https://ci.appveyor.com/project/jeking3/property-map/build/1.0.2-develop/job/9tt6av7lxe14qy8h  
  
Is this a known issue?

---

## Comment 1

> Username: jeking3  
> Created at: 2018-05-19 00:53:33 UTC  
> Updated at: 2018-05-19 01:08:00 UTC  
> Url: https://github.com/boostorg/test/issues/144#issuecomment-390366377  

This appears to happen only when using `cxxstd=03`.  By adding `cxxstd-dialect=gnu` this issue can be worked around for cygwin.  To be more restrictive, you can use `define=_POSIX_C_SOURCE=200112L`.  Perhaps this should be mentioned in the documentation somewhere, or perhaps boost jam should set this automatically.

---

## Comment 2

> Username: raffienficiaud  
> Created at: 2018-05-19 13:56:58 UTC  
> Updated at: 2018-05-19 13:57:24 UTC  
> Url: https://github.com/boostorg/test/issues/144#issuecomment-390406684  

Thanks for the report. It would be good that you add those issues on Trac - as explained in the contributions document -:  there are some compilation errors already reported by other people. I believe the one of ``::mkstemp`` is already there.   
  
I would be interested in any patch, since I do not have any cygwin knowledge. Thanks.  
  
PS: I'll close this once you create/comment on trac if you do not mind.

---

## Comment 3

> Username: jeking3  
> Created at: 2018-05-19 14:00:15 UTC  
> Url: https://github.com/boostorg/test/issues/144#issuecomment-390406892  

I will work on a patch that fixes this on cygwin.  Too bad we cannot all use github issues. :)

---

## Comment 4

> Username: jeking3  
> Created at: 2018-05-19 18:15:03 UTC  
> Url: https://github.com/boostorg/test/issues/144#issuecomment-390423039  

So in general here, I think it would be best to check the value of __POSIX_VISIBLE if the platform is CYGWIN and output a better error message, telling folks they need to define _POSIX_C_SOURCE=200112L in order to use Boost.Test.  If the cygwin headers are included in the compilation unit first then Boost.Test cannot change them.

---

## Comment 5

> Username: jeking3  
> Created at: 2018-05-20 01:56:27 UTC  
> Url: https://github.com/boostorg/test/issues/144#issuecomment-390452001  

By adding a comment to the code that fails to compile, it makes it a lot easier for someone to understand what happened and how to fix it:  
```  
$ ../../b2 -q cxxstd=03  
Performing configuration checks  
...found 4452 targets...  
...updating 458 targets...  
gcc.compile.c++ ../../bin.v2/libs/test/build/gcc-6.4.0/debug/cxxstd-03-iso/link-static/debug.o  
In file included from ../../libs/test/src/debug.cpp:16:0:  
../../boost/test/impl/debug.ipp: In function ‘const char* boost::debug::{anonymous}::prepare_gdb_cmnd_file(const boost::debug::dbg_startup_info&)’:  
../../boost/test/impl/debug.ipp:424:23: error: ‘::mkstemp’ has not been declared  
     fd_holder cmd_fd( ::mkstemp( cmd_file_name ) ); // if mkstemp is not defined on Cygwin, define _POSIX_C_SOURCE appropriately  
                       ^~  
In file included from ../../libs/test/src/debug.cpp:16:0:  
../../boost/test/impl/debug.ipp: In function ‘void boost::debug::debugger_break()’:  
../../boost/test/impl/debug.ipp:711:5: error: ‘::kill’ has not been declared  
     ::kill( ::getpid(), SIGTRAP );  
     ^~  
../../boost/test/impl/debug.ipp: In function ‘bool boost::debug::attach_debugger(bool)’:  
../../boost/test/impl/debug.ipp:892:34: error: ‘::mkstemp’ has not been declared  
     fd_holder init_done_lock_fd( ::mkstemp( init_done_lock_fn ) );  
                                  ^~  
../../boost/test/impl/debug.ipp:929:9: error: ‘::select’ has not been declared  
         ::select( 0, 0, 0, 0, &to );  
         ^~  
```  
  
Normally I would submit a PR, but folks should be able to figure this one out on their own.  A note in the documentation about cygwin seems appropriate however.  In any case I'm going to close this.  In each of the affected repositories I will set this macro as needed.
