# #532 - Warning while building example [Closed]

> Username: octopus-prime  
> Created at: 2017-06-23 16:48:53 UTC  
> Updated at: 2017-08-12 20:08:16 UTC  
> Closed at: 2017-08-12 20:08:16 UTC  
> Url: https://github.com/boostorg/beast/issues/532  

Building this example (using Beast/65)  
https://github.com/vinniefalco/Beast/blob/master/example/websocket-client-ssl/websocket_client_ssl.cpp  
with  
`gcc 7.0.1 and -std=c++17`  
shows this warnings:  
```  
gcc.compile.c++ bin/gcc-7/release/example/foo.o  
  
    "g++"  -ftemplate-depth-128 -march=native -ftemplate-depth=256 -std=c++17 -O3 -finline-functions -Wno-inline -Wall -fPIC  -DBOOST_ALL_DYN_LINK -DNDEBUG  -I"/home/mike/workspace/Beast/include" -I"include" -c -o "bin/gcc-7/release/example/foo.o" "example/foo.cpp"  
  
In file included from /home/mike/workspace/Beast/include/beast/zlib/deflate_stream.hpp:41:0,  
                 from /home/mike/workspace/Beast/include/beast/websocket/detail/pmd_extension.hpp:14,  
                 from /home/mike/workspace/Beast/include/beast/websocket/detail/stream_base.hpp:17,  
                 from /home/mike/workspace/Beast/include/beast/websocket/stream.hpp:14,  
                 from /home/mike/workspace/Beast/include/beast/websocket.hpp:16,  
                 from example/foo.cpp:174:  
/home/mike/workspace/Beast/include/beast/zlib/detail/deflate_stream.hpp: In member function ‘void beast::zlib::detail::deflate_stream::doWrite(beast::zlib::z_params&, beast::zlib::Flush, beast::error_code&) [with <template-parameter-1-1> = void]’:  
/home/mike/workspace/Beast/include/beast/zlib/detail/deflate_stream.hpp:1056:30: warning: ‘*((void*)& old_flush +4)’ may be used uninitialized in this function [-Wmaybe-uninitialized]  
     else if(zs.avail_in == 0 && flush <= old_flush &&  
             ~~~~~~~~~~~~~~~~~^~~~~~~~~~~~~~~~~~~~~  
gcc.link bin/gcc-7/release/hessian-example  
```

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-06-23 16:52:59 UTC  
> Url: https://github.com/boostorg/beast/issues/532#issuecomment-310717766  

Yeah I don't really know how to fix that, its a false positive - do you have any suggestions?

---

## Comment 2

> Username: vinniefalco  
> Created at: 2017-06-23 17:35:56 UTC  
> Url: https://github.com/boostorg/beast/issues/532#issuecomment-310727744  

I'm investigating, it does not happen on clang, msvc, or gcc 6:  
https://travis-ci.org/vinniefalco/Beast/jobs/246285814  
  
It does happen on gcc 5   
https://travis-ci.org/vinniefalco/Beast/jobs/246285811

---

## Comment 3

> Username: octopus-prime  
> Created at: 2017-06-23 17:53:39 UTC  
> Url: https://github.com/boostorg/beast/issues/532#issuecomment-310731708  

There you have a comparison  
`flush <= old_flush`  
comparing  
`Flush` and `boost::optional<Flush>`  
  
I am not sure and the doc  
http://www.boost.org/doc/libs/1_64_0/libs/optional/doc/html/boost_optional/tutorial/relational_operators.html  
is not that clear about the mixed type comparison.  
  
So the basic idea is to change the type of variable `flush` to `boost::optional<Flush>` too.  
By changing the signature of `doWrite` to  
`doWrite(z_params& zs, boost::optional<Flush> flush, error_code& ec)`  
and modifying this block  
```  
        switch(strategy_)  
        {  
        case Strategy::huffman:  
            bstate = deflate_huff(zs, flush.get());  
            break;  
        case Strategy::rle:  
            bstate = deflate_rle(zs, flush.get());  
            break;  
        default:  
        {  
            bstate = (this->*(get_config(level_).func))(zs, flush.get());  
            break;  
        }  
        }  
```  
  
Have a look at the uploaded file. Compiles without warning.  
  
[deflate_stream.txt](https://github.com/vinniefalco/Beast/files/1098604/deflate_stream.txt)

---

## Comment 4

> Username: vinniefalco  
> Created at: 2017-06-23 17:55:04 UTC  
> Url: https://github.com/boostorg/beast/issues/532#issuecomment-310732032  

Very interesting!

---

## Comment 5

> Username: vinniefalco  
> Created at: 2017-06-23 18:17:54 UTC  
> Url: https://github.com/boostorg/beast/issues/532#issuecomment-310737457  

On gcc 5 it did not fix the problem:  
https://travis-ci.org/vinniefalco/Beast/jobs/246301785#L1338  
  
https://github.com/vinniefalco/Beast/commit/b8342181de825aae688834b68ced66e0165385de

---

## Comment 6

> Username: octopus-prime  
> Created at: 2017-06-23 18:26:25 UTC  
> Updated at: 2017-06-23 18:26:44 UTC  
> Url: https://github.com/boostorg/beast/issues/532#issuecomment-310739502  

Tried your workaround with `boost::optional<Flush> flush = flush_param`.  
Get warnings too.  
  
So try my approach. With `boost::optional<Flush> flush` as argument in `doWrite()`

---

## Comment 7

> Username: octopus-prime  
> Created at: 2017-06-23 18:36:05 UTC  
> Url: https://github.com/boostorg/beast/issues/532#issuecomment-310741834  

BTW: Have you inspected boost iostream zlib?  
http://www.boost.org/doc/libs/1_64_0/libs/iostreams/doc/classes/zlib.html

---

## Comment 8

> Username: vinniefalco  
> Created at: 2017-06-23 18:37:53 UTC  
> Url: https://github.com/boostorg/beast/issues/532#issuecomment-310742264  

Yes, that requires that you link against zlib. The whole point of the zlib port in Beast is that 1. It is c++11 with type safety, and 2. it is header-only, does not require building a linking a separate lib.

---

## Comment 9

> Username: octopus-prime  
> Created at: 2017-06-23 18:41:45 UTC  
> Updated at: 2017-06-23 18:52:31 UTC  
> Url: https://github.com/boostorg/beast/issues/532#issuecomment-310743153  

But header-only is not true for ssl?!  
So using ssl needs linking openssl. ssl is optional.  
  
Perhaps using zlib needs linking zlib? is zlib optional too?  
  
You know... 3000 lines of code. And reinvent the wheel... Or use a lib.

---

## Comment 10

> Username: octopus-prime  
> Created at: 2017-06-23 20:01:44 UTC  
> Url: https://github.com/boostorg/beast/issues/532#issuecomment-310759820  

But serious. Beast is not header-only at all.  
  
Always requiered  
```  
/site-config//boost_system  
/site-config//pthread  
```  
  
Optional  
```  
/site-config//boost_coroutine  
/site-config//crypto  
/site-config//ssl  
```  
  
So, who cares about one optional lib more? And for what a price?  
boost::iostream decided to not re-implement zlib too but use the optional library dependency to zlib...  
  
But you decided to maintain zlib support by yourself. We will respect it.  
And of course the poor windows developers (no apt, no yum, no brew) will love you :-)

---

## Comment 11

> Username: octopus-prime  
> Created at: 2017-06-23 20:03:35 UTC  
> Url: https://github.com/boostorg/beast/issues/532#issuecomment-310760156  

Did the approach with `boost::optional<Flush> flush` as argument in `doWrite()` work?

---

## Comment 12

> Username: vinniefalco  
> Created at: 2017-06-23 20:10:28 UTC  
> Updated at: 2017-06-23 20:12:25 UTC  
> Url: https://github.com/boostorg/beast/issues/532#issuecomment-310761496  

>And of course the poor windows developers (no apt, no yum, no brew) will love you :-)  
  
I'm one of those Windows developers!  
  
"header-only" means that Beast does not come with a build script for building its library. So in that sense, it is very much header-only. The very first complaint about Boost.Http (a library that was reviewed in 2015) was "this is not header-only." There is a preference for header-only libraries, they are easier to maintain and easier to integrate. So that is why I made those choices.  
  
Do note that Beast's zlib implementation is a port and not a rewrite of zlib.

---

## Comment 13

> Username: octopus-prime  
> Created at: 2017-06-23 20:19:11 UTC  
> Url: https://github.com/boostorg/beast/issues/532#issuecomment-310763378  

Yes. I thought so.  
And I KNOW the pain with 3rd party libs on windows - believe me ;-)  
So i always feel poor, if i have to do jobs on windows...  
Building 3rd party libs for example via cygwin, configure and make... :-(  
  
Again, your decision to include zlib stuff is very fine!  
If i have to use beast on windows i will be very very very happy :-)

---

## Comment 14

> Username: octopus-prime  
> Created at: 2017-06-23 21:16:27 UTC  
> Url: https://github.com/boostorg/beast/issues/532#issuecomment-310775015  

In less than a month beast is in boost.  
So zlib is in boost without dependency.  
So your implementation should be in iostreams too.

---

## Comment 15

> Username: vinniefalco  
> Created at: 2017-06-23 21:31:13 UTC  
> Url: https://github.com/boostorg/beast/issues/532#issuecomment-310777741  

Nope  
https://travis-ci.org/vinniefalco/Beast/jobs/246313028#L1303

---

## Comment 16

> Username: vinniefalco  
> Created at: 2017-06-23 21:32:31 UTC  
> Url: https://github.com/boostorg/beast/issues/532#issuecomment-310777979  

>So your implementation should be in iostreams too.  
  
The Beast version of zlib does not support the gzip wrapper, I removed it because WebSocket doesn't need it. I will add it back at some point, but it needs more extensive tests. I don't think iostreams should use the zlib from Beast yet.

---

## Comment 17

> Username: vinniefalco  
> Created at: 2017-07-04 01:51:23 UTC  
> Url: https://github.com/boostorg/beast/issues/532#issuecomment-312761570  

Is this resolved?  
  
>

---

## Comment 18

> Username: vinniefalco  
> Created at: 2017-08-12 20:08:16 UTC  
> Url: https://github.com/boostorg/beast/issues/532#issuecomment-322002936  

It looks like this has been resolved so I will close the issue - thanks!
