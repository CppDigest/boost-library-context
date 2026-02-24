# #2648 - Clang 16 regression causes compilation error in ssl_stream in C++20 mode [Closed]

> Username: vient  
> Created at: 2023-03-02 09:05:01 UTC  
> Updated at: 2024-06-06 05:33:32 UTC  
> Closed at: 2024-01-03 08:46:17 UTC  
> Url: https://github.com/boostorg/beast/issues/2648  

Hello, I want to notify you about https://github.com/llvm/llvm-project/issues/60749  
  
This is a problem in Clang itself but they decided that it is not a release blocker. Clang 16 will be released in a week and [provided code](https://godbolt.org/z/nzePcnGjj) is not compiling. Is it possible to change something on Beast side? It seems that the bug requires very specific conditions.  
  
Sorry for inconvenience, we want to switch to LLVM 16 asap and I'm not sure what to do in light of this bug.

---

## Comment 1

> Username: vient  
> Created at: 2023-04-18 06:18:23 UTC  
> Url: https://github.com/boostorg/beast/issues/2648#issuecomment-1512500479  

FYI  
For our purposes we just made this small patch, after that Beast compiles fine  
```diff  
diff --git a/boost/beast/ssl/ssl_stream.hpp b/boost/beast/ssl/ssl_stream.hpp  
index d7ce7c1..51cc9d0 100644  
--- a/boost/beast/ssl/ssl_stream.hpp  
+++ b/boost/beast/ssl/ssl_stream.hpp  
@@ -673,7 +673,7 @@ public:  
         ssl_stream<SyncStream>& stream,  
         boost::system::error_code& ec);  
  
-    template<class AsyncStream, BOOST_BEAST_ASYNC_TPARAM1 TeardownHandler>  
+    template<class AsyncStream, class TeardownHandler>  
     friend  
     void  
     async_teardown(  
@@ -697,7 +697,7 @@ teardown(  
 }  
  
 template<class AsyncStream,  
-        BOOST_BEAST_ASYNC_TPARAM1 TeardownHandler = net::default_completion_token_t<beast::executor_type<AsyncStream>>>  
+        class TeardownHandler = net::default_completion_token_t<beast::executor_type<AsyncStream>>>  
 void  
 async_teardown(  
     boost::beast::role_type role,  
 ```  
Loosening requirements is not good but Clang bug lies exactly in applying concepts in template list so removing concepts helps. In original issue someone mentioned that you can also move concept to requires clause, like `template<Concept x>` to `template<class x> requires Concept(x)` or something similar, but I did not manage to get working version of this.

---

## Comment 2

> Username: Tectu  
> Created at: 2023-08-22 22:45:14 UTC  
> Updated at: 2023-08-22 22:46:28 UTC  
> Url: https://github.com/boostorg/beast/issues/2648#issuecomment-1689021981  

Am I correct that a corresponding fix did not make it into 1.83 despite the assigned milestone?

---

## Comment 3

> Username: stromnet  
> Created at: 2023-09-05 07:23:16 UTC  
> Url: https://github.com/boostorg/beast/issues/2648#issuecomment-1706079638  

Archlinux has bumped to clang 16 now, causing this error to pop up on my local development machine.  clang15 package is available in extras, but would be nice to be able to build with clang 16 too.

---

## Comment 4

> Username: Tectu  
> Created at: 2023-09-05 09:54:27 UTC  
> Url: https://github.com/boostorg/beast/issues/2648#issuecomment-1706306604  

Similar issue over at FreeBSD and most likely several other places too. I'm not involved in boost development but maybe this would justify a patch release (i.e. boost `1.83.1`)?

---

## Comment 5

> Username: klemens-morgenstern  
> Created at: 2023-09-05 10:24:02 UTC  
> Url: https://github.com/boostorg/beast/issues/2648#issuecomment-1706350979  

Yeah, I forgot to merge in time. Issue is fixed in develop. Really sorry about that.

---

## Comment 6

> Username: Tectu  
> Created at: 2023-09-05 13:30:32 UTC  
> Url: https://github.com/boostorg/beast/issues/2648#issuecomment-1706627941  

> Yeah, I forgot to merge in time. Issue is fixed in develop. Really sorry about that.  
  
Could you share a link to the particular commit so downstream package maintainers could potentially ship the patch if no `1.83.1` release is planned to address this issue (is there?)?

---

## Comment 7

> Username: vient  
> Created at: 2023-09-05 17:46:34 UTC  
> Url: https://github.com/boostorg/beast/issues/2648#issuecomment-1707045345  

https://github.com/boostorg/beast/commit/72c2eeb3980ed0dc530de9241e25b247517de018
