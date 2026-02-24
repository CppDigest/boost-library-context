# #967 - how to fix "fatal error: boost/beast/core/detail/config.hpp: No such file or directory" [Closed]

> Username: Binsoma  
> Created at: 2018-01-04 15:47:41 UTC  
> Updated at: 2019-07-04 09:03:58 UTC  
> Closed at: 2018-01-09 16:42:14 UTC  
> Url: https://github.com/boostorg/beast/issues/967  

Hi there,  
I have boost c++ libraries in /usr/local/boost_1_66_0/stage/include/boost/  
  
I am trying to compile a c++ example and i'm getting fatal errors.   
how to make a compiler see everything exact location?   
  thanks

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-01-04 15:57:24 UTC  
> Url: https://github.com/boostorg/beast/issues/967#issuecomment-355319548  

Can you please provide more information? What build system are you using?

---

## Comment 2

> Username: Binsoma  
> Created at: 2018-01-04 16:00:56 UTC  
> Url: https://github.com/boostorg/beast/issues/967#issuecomment-355320673  

I'm using debian with boost 1.66.0. I have a Boost Library Binary at /usr/local/boost_1_66_0/stage/include/boost/

---

## Comment 3

> Username: Binsoma  
> Created at: 2018-01-04 16:02:38 UTC  
> Updated at: 2018-01-04 16:09:32 UTC  
> Url: https://github.com/boostorg/beast/issues/967#issuecomment-355321160  

I was compiling Websocket client example from " http://www.boost.org/doc/libs/master/libs/beast/doc/html/beast/quick_start.html

---

## Comment 4

> Username: vinniefalco  
> Created at: 2018-01-04 16:09:24 UTC  
> Url: https://github.com/boostorg/beast/issues/967#issuecomment-355323111  

So you're using bjam? make? CMake?

---

## Comment 5

> Username: Binsoma  
> Created at: 2018-01-04 16:11:59 UTC  
> Updated at: 2018-01-04 16:12:11 UTC  
> Url: https://github.com/boostorg/beast/issues/967#issuecomment-355323890  

I used ./bootstrap.sh and ./b2 install from "http://www.boost.org/doc/libs/1_66_0/more/getting_started/unix-variants.html  
"

---

## Comment 6

> Username: cmazakas  
> Created at: 2018-01-04 16:25:25 UTC  
> Url: https://github.com/boostorg/beast/issues/967#issuecomment-355327668  

It sounds like you need to add the Boost include directory to your compiler's list of include paths.  
  
Make sure you compile with something like `-I/usr/local/boost_1_66_0/stage/include`

---

## Comment 7

> Username: Binsoma  
> Created at: 2018-01-04 16:31:24 UTC  
> Url: https://github.com/boostorg/beast/issues/967#issuecomment-355329314  

now I'm getting this error "/usr/local/boost_1_66_0/stage/include/boost/beast/core/impl/buffers_adapter.ipp: In member function ‘boost::beast::buffers_adapter<MutableBufferSequence>::const_buffers_type::const_iterator& boost::beast::buffers_adapter<MutableBufferSequence>::const_buffers_type::const_iterator::operator--()’:  
/usr/local/boost_1_66_0/stage/include/boost/beast/core/impl/buffers_adapter.ipp:121:11: error: ‘it_’ was not declared in this scope  
         --it_;"

---

## Comment 8

> Username: Binsoma  
> Created at: 2018-01-04 16:32:13 UTC  
> Url: https://github.com/boostorg/beast/issues/967#issuecomment-355329542  

In function `boost::asio::detail::posix_thread::start_thread(boost::asio::detail::posix_thread::func_base*)':  
websocketclient.cpp:(.text._ZN5boost4asio6detail12posix_thread12start_threadEPNS2_9func_baseE[_ZN5boost4asio6detail12posix_thread12start_threadEPNS2_9func_baseE]+0x29): undefined reference to `pthread_create'  
collect2: error: ld returned 1 exit status

---

## Comment 9

> Username: cmazakas  
> Created at: 2018-01-04 16:35:59 UTC  
> Url: https://github.com/boostorg/beast/issues/967#issuecomment-355330582  

This makes good sense.  
  
Trying using `-pthread` as well. You may also have to link to some specific Boost libraries as well.  
  
Make sure you re-read the documentation in Boost. They give some examples of how to link to some Boost libraries. You'll need to do this as well.

---

## Comment 10

> Username: vinniefalco  
> Created at: 2018-01-09 16:42:14 UTC  
> Url: https://github.com/boostorg/beast/issues/967#issuecomment-356341100  

Has this issue been resolved?

---

## Comment 11

> Username: Pernath  
> Created at: 2019-01-27 06:12:09 UTC  
> Url: https://github.com/boostorg/beast/issues/967#issuecomment-457892167  

Apparently it has not. Why is it closed? It is not old or unrelatable at all.

---

## Comment 12

> Username: vinniefalco  
> Created at: 2019-01-27 07:07:13 UTC  
> Url: https://github.com/boostorg/beast/issues/967#issuecomment-457894318  

There was nothing actionable here for me (i.e. no changes in beast were required), so the issue could safely be closed. Note that closed issues can still receive replies, which would be appropriate here, except that the OP hasn't responded in over a year which is fairly strong evidence that either they figured it out, or gave up.

---

## Comment 13

> Username: nikitasius  
> Created at: 2019-07-04 09:00:34 UTC  
> Updated at: 2019-07-04 09:03:58 UTC  
> Url: https://github.com/boostorg/beast/issues/967#issuecomment-508402576  

Thats how i fond you:  
```  
HEAD is now at 3a1f602... Fix CircleCI error on wrong indentation (#662)  
[ 52%] Performing patch step for 'soci'  
[ 53%] Performing update step for 'soci'  
[ 54%] Performing configure step for 'soci'  
-- soci configure command succeeded.  See also /home/nikitas/crypto/xrp/rippled/.nih_c/unix_makefiles/GNU_6.3.0/Release/src/soci-stamp/soci-configure-*.log  
[ 55%] Performing build step for 'soci'  
-- soci build command succeeded.  See also /home/nikitas/crypto/xrp/rippled/.nih_c/unix_makefiles/GNU_6.3.0/Release/src/soci-stamp/soci-build-*.log  
[ 55%] No install step for 'soci'  
[ 56%] No test step for 'soci'  
[ 57%] Completed 'soci'  
[ 57%] Built target soci  
Scanning dependencies of target rippled  
[ 58%] Building CXX object CMakeFiles/rippled.dir/src/ripple/unity/app_consensus.cpp.o  
In file included from /home/nikitas/crypto/xrp/rippled/src/ripple/beast/container/aged_unordered_map.h:23:0,  
                 from /home/nikitas/crypto/xrp/rippled/src/ripple/ledger/CachedSLEs.h:25,  
                 from /home/nikitas/crypto/xrp/rippled/src/ripple/ledger/CachedView.h:23,  
                 from /home/nikitas/crypto/xrp/rippled/src/ripple/app/ledger/Ledger.h:25,  
                 from /home/nikitas/crypto/xrp/rippled/src/ripple/app/consensus/RCLCxLedger.h:23,  
                 from /home/nikitas/crypto/xrp/rippled/src/ripple/app/consensus/RCLConsensus.h:23,  
                 from /home/nikitas/crypto/xrp/rippled/src/ripple/app/consensus/RCLConsensus.cpp:20,  
                 from /home/nikitas/crypto/xrp/rippled/src/ripple/unity/app_consensus.cpp:20:  
/home/nikitas/crypto/xrp/rippled/src/ripple/beast/container/detail/aged_unordered_container.h:27:63: fatal error: boost/beast/core/detail/empty_base_optimization.hpp: No such file or directory  
 #include <boost/beast/core/detail/empty_base_optimization.hpp>  
                                                               ^  
compilation terminated.  
CMakeFiles/rippled.dir/build.make:62: recipe for target 'CMakeFiles/rippled.dir/src/ripple/unity/app_consensus.cpp.o' failed  
make[2]: *** [CMakeFiles/rippled.dir/src/ripple/unity/app_consensus.cpp.o] Error 1  
CMakeFiles/Makefile2:132: recipe for target 'CMakeFiles/rippled.dir/all' failed  
make[1]: *** [CMakeFiles/rippled.dir/all] Error 2  
Makefile:129: recipe for target 'all' failed  
make: *** [all] Error 2  
```  
  
line 27:  
* `#include <boost/beast/core/detail/empty_base_optimization.hpp>`  
  
where:  
```  
find . -name *empty_base_optimization*  
./rippled/.nih_c/unix_makefiles/GNU_6.3.0/Release/nudb_src-src/extras/beast/include/beast/core/detail/empty_base_optimization.hpp  
./rippled/.nih_c/unix_makefiles/GNU_6.3.0/Release/nudb_src-src/extras/beast/test/core/empty_base_optimization.cpp  
```  
  
Guess it's rippled's problem.  
  
upd: they say what rippled should use 1.67 version exact :confused:  https://github.com/ripple/rippled/issues/2991#issuecomment-506734416
