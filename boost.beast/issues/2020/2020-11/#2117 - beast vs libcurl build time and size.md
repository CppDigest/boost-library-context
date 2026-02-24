# #2117 - beast vs libcurl build time and size [Closed]

> Username: AlexandreBossard  
> Created at: 2020-11-05 16:36:40 UTC  
> Updated at: 2021-05-29 17:10:54 UTC  
> Closed at: 2021-05-29 17:10:54 UTC  
> Url: https://github.com/boostorg/beast/issues/2117  

After some rambling on the #boost slack, I went and used the simplest examples I could use of libcurl and beast to make a comparison of build time, but most importantly binary size.  
I've used these 3 examples:  
- simple_curl: https://github.com/curl/curl/blob/master/docs/examples/simple.c  
- simple_beast: https://github.com/boostorg/beast/blob/develop/example/http/client/sync-ssl/http_client_sync_ssl.cpp  
- async_beast: https://github.com/boostorg/beast/blob/develop/example/http/client/async-ssl/http_client_async_ssl.cpp  
(I have only modified the main() parameters to use the same target)  
  
* simple_curl takes 100ms to compile on my machine  
* simple_beast takes 6s  
* async_beast takes 6,1s   
  
binaries are statically linked:  
```  
bin/simple_curl:  
	linux-vdso.so.1 (0x00007ffced3e3000)  
	libdl.so.2 => /lib/x86_64-linux-gnu/libdl.so.2 (0x00007f8f2224f000)  
	libpthread.so.0 => /lib/x86_64-linux-gnu/libpthread.so.0 (0x00007f8f2222c000)  
	libc.so.6 => /lib/x86_64-linux-gnu/libc.so.6 (0x00007f8f2203a000)  
	/lib64/ld-linux-x86-64.so.2 (0x00007f8f22602000)  
bin/simple_beast:  
	linux-vdso.so.1 (0x00007ffd057f6000)  
	libdl.so.2 => /lib/x86_64-linux-gnu/libdl.so.2 (0x00007f966416e000)  
	libpthread.so.0 => /lib/x86_64-linux-gnu/libpthread.so.0 (0x00007f966414b000)  
	libstdc++.so.6 => /lib/x86_64-linux-gnu/libstdc++.so.6 (0x00007f9663f6a000)  
	libgcc_s.so.1 => /lib/x86_64-linux-gnu/libgcc_s.so.1 (0x00007f9663f4f000)  
	libc.so.6 => /lib/x86_64-linux-gnu/libc.so.6 (0x00007f9663d5d000)  
	/lib64/ld-linux-x86-64.so.2 (0x00007f96644f6000)  
	libm.so.6 => /lib/x86_64-linux-gnu/libm.so.6 (0x00007f9663c0e000)  
bin/async_beast:  
	linux-vdso.so.1 (0x00007ffd1f9f5000)  
	libdl.so.2 => /lib/x86_64-linux-gnu/libdl.so.2 (0x00007fe606c6f000)  
	libpthread.so.0 => /lib/x86_64-linux-gnu/libpthread.so.0 (0x00007fe606c4c000)  
	libstdc++.so.6 => /lib/x86_64-linux-gnu/libstdc++.so.6 (0x00007fe606a6b000)  
	libgcc_s.so.1 => /lib/x86_64-linux-gnu/libgcc_s.so.1 (0x00007fe606a50000)  
	libc.so.6 => /lib/x86_64-linux-gnu/libc.so.6 (0x00007fe60685e000)  
	/lib64/ld-linux-x86-64.so.2 (0x00007fe607048000)  
	libm.so.6 => /lib/x86_64-linux-gnu/libm.so.6 (0x00007fe60670f000)  
```  
  
compilation  size:  
```  
 ls -l bin/*  
-rwxrwxr-x 1 alex alex 5289352 nov.   5 16:25 bin/async_beast*  
-rwxrwxr-x 1 alex alex 4123888 nov.   5 16:24 bin/simple_beast*  
-rwxrwxr-x 1 alex alex 4154688 nov.   5 16:25 bin/simple_curl*  
```  
after calling strip:  
```  
$ ls -l bin/*  
-rwxrwxr-x 1 alex alex 3871992 nov.   5 16:29 bin/async_beast*  
-rwxrwxr-x 1 alex alex 3540216 nov.   5 16:29 bin/simple_beast*  
-rwxrwxr-x 1 alex alex 3734976 nov.   5 16:29 bin/simple_curl*  
```  
  
So, the difference does not seem much. One can argue that those examples are too simple and small to make some conclusions.  
But, we have to note that, libcurl is a full fledged http1 2 and 3 client. It has support for url resolution, http keep alive, multi session and probably more.  
Beast has none of that (by design, I know) but it still manages to be bigger and takes AGES to compile.  
Plus, as soon as you start to use more beast features, like use more than one message body (vector/string/dynamic_body) the size grows rapidly.  
by just adding this (broken diff)  
```  
--- a/beast/async.cpp  
+++ b/beast/async.cpp  
@@ -1,6 +1,7 @@  
 #include <boost/beast/core.hpp>  
 #include <boost/beast/http.hpp>  
 #include <boost/beast/ssl.hpp>  
+#include <boost/beast/http/vector_body.hpp>  
 #include <boost/beast/version.hpp>  
 #include <boost/asio/strand.hpp>  
 #include <cstdlib>  
@@ -33,6 +34,9 @@ class session : public std::enable_shared_from_this<session>  
     http::request<http::empty_body> req_;  
     http::response<http::string_body> res_;  
   
+    http::request<http::vector_body<char>> req2_;  
+    http::response<http::vector_body<char>> res2_;  
+  
 public:  
     explicit  
     session(  
@@ -122,6 +126,11 @@ public:  
             beast::bind_front_handler(  
                 &session::on_write,  
                 shared_from_this()));  
+  
+        http::async_write(stream_, req2_,  
+            beast::bind_front_handler(  
+                &session::on_write,  
+                shared_from_this()));  
     }  
   
     void  
@@ -139,6 +148,10 @@ public:  
             beast::bind_front_handler(  
                 &session::on_read,  
                 shared_from_this()));  
+        http::async_read(stream_, buffer_, res2_,  
+            beast::bind_front_handler(  
+                &session::on_read,  
+                shared_from_this()));  
     }  
```  
  
```  
-rwxrwxr-x 1 alex alex 6348328 nov.   5 17:06 bin/async_beast*  
```  
then stripped:  
```  
-rwxrwxr-x 1 alex alex 4179192 nov.   5 17:05 bin/async_beast*  
```  
we increased our stripped size by 307KiB !  
  
/rant  
The most egregious thing, is when you want to actually write some kind of http client, you start to use composed operations which are really hard to get right and can generate a lot of template code if you don't really think hard about what your doing. Throw an URL library (skyr fex), a json library and Outcome for public api error and you end up asking why your brand new cpu feels so slow this morning. Or why you mingw build fails because of Object fi being to big. You gdb taking ages to load the boost symbols, etc...  
/rant  
  
What is this all about?  
Beast is a pretty good library, i like it. It's documentation is miles better than asio's, and it does what it set up to do. And for me, It's asynchronous (asio's actually) model is what me choose it. It's C++, it should be better than C right ?  
  
Problem is, libcurl is a very very good library, widely use, widely understand, as much as its api suck.  
  
It is really hard to justify Beast and Boost when you have such a good alternative, even behind some kind of c++ wrapper. I'm not here to blame anyone or anything. But the cost of entry for usin Beast (and asio) is really high, and its getting higher every turn you make for features.  
  
Do you have any solutions, or at least plans to mitigate beast compilation times and binary sizes ? Is it even possible ?

---

## Comment 1

> Username: vinniefalco  
> Created at: 2020-11-05 16:41:55 UTC  
> Url: https://github.com/boostorg/beast/issues/2117#issuecomment-722495363  

Yeah I agree that it could be better. Note that curl doesn't help if you are implementing a server.

---

## Comment 2

> Username: AlexandreBossard  
> Created at: 2020-11-05 17:02:04 UTC  
> Url: https://github.com/boostorg/beast/issues/2117#issuecomment-722507378  

I know, but that's besides the point. I'm sure someone could leverage libcurl code to make a beast like library in C for server purpose.  
C++ eco system is for far beyond the http hype. beast is, (IMHO) the best attempt to close that gap. But it's still not enough.

---

## Comment 3

> Username: stale[bot]  
> Created at: 2020-12-25 12:12:36 UTC  
> Url: https://github.com/boostorg/beast/issues/2117#issuecomment-751240910  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 4

> Username: stale[bot]  
> Created at: 2021-05-29 17:10:49 UTC  
> Url: https://github.com/boostorg/beast/issues/2117#issuecomment-850866709  

It looks like this issue has either been abandoned or resolved so I will go ahead and close it. Feel free to re-open this issue if you feel it needs attention, or open new issues as needed. Thanks!
