# #1786 - write_some_win32_op does not respect serializer::split() state [Closed]

> Username: mika-fischer  
> Created at: 2019-12-20 18:02:20 UTC  
> Updated at: 2020-12-04 21:09:37 UTC  
> Closed at: 2019-12-23 19:16:00 UTC  
> Url: https://github.com/boostorg/beast/issues/1786  

### Version of Beast  
  
266  
  
### Steps necessary to reproduce the problem  
  
Call `async_write_header(stream, serializer, handler)` with a `serializer<true, basic_file_body<file_win32>>` and the whole request including the body will be transfered and only then will the handler be called.  
  
In https://github.com/boostorg/beast/blob/develop/include/boost/beast/http/impl/file_body_win32.hpp#L369 the split state is overwritten and the behavior of the op is as if it was false (i.e. it will continue to transfer the body), see https://github.com/boostorg/beast/blob/develop/include/boost/beast/http/impl/file_body_win32.hpp#L421-L425  
  
Instead, the original state of split should be stored and the op should complete after sending the header if split was set when the op was initiated.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2019-12-20 18:03:55 UTC  
> Url: https://github.com/boostorg/beast/issues/1786#issuecomment-568022516  

I agree. Would you like to attempt a fix?

---

## Comment 2

> Username: mika-fischer  
> Created at: 2019-12-20 18:19:04 UTC  
> Url: https://github.com/boostorg/beast/issues/1786#issuecomment-568033691  

I'll see if I can get it done on Monday.

---

## Comment 3

> Username: mika-fischer  
> Created at: 2019-12-20 18:43:29 UTC  
> Url: https://github.com/boostorg/beast/issues/1786#issuecomment-568040580  

This works for me. Do you want me to create a pull request (will have to wait until Monday)?  
  
  
```patch  
diff --git a/file_body_win32.hpp.orig b/file_body_win32.hpp  
index cfeb5aa..6d9fe5f 100644  
--- a/file_body_win32.hpp.orig  
+++ b/file_body_win32.hpp  
@@ -340,6 +340,7 @@ class write_some_win32_op  
         basic_file_body<file_win32>, Fields>& sr_;  
     std::size_t bytes_transferred_ = 0;  
     bool header_ = false;  
+    bool only_header_;  
  
 public:  
     template<class Handler_>  
@@ -355,6 +356,7 @@ public:  
                 s.get_executor())  
         , sock_(s)  
         , sr_(sr)  
+        , only_header_(sr.split() && !sr.is_header_done())  
     {  
         (*this)();  
     }  
@@ -415,7 +417,7 @@ public:  
         std::size_t bytes_transferred = 0)  
     {  
         bytes_transferred_ += bytes_transferred;  
-        if(! ec)  
+        if(!ec && (!sr_.is_header_done() || !only_header_))  
         {  
             if(header_)  
             {  
```

---

## Comment 4

> Username: vinniefalco  
> Created at: 2019-12-20 18:44:35 UTC  
> Url: https://github.com/boostorg/beast/issues/1786#issuecomment-568040869  

LOL.. is that it??? Sure, open a pull request. And there's no rush, the next version of Boost doesn't go out until April 2020. Waiting until Monday or even a week after that is no problem at all - thanks!

---

## Comment 5

> Username: mika-fischer  
> Created at: 2019-12-23 14:55:06 UTC  
> Url: https://github.com/boostorg/beast/issues/1786#issuecomment-568496331  

I'm a bit unsure what the expected behavior actually is:  
- With `split == true`, it's clear that we may only serialize the header. But is it OK to write less than the full header?  
- With `split == false` we split it internally anyway, so then is it OK to also return after serializing the header? Or is it somehow important that we also start serializing the body right away?  
  
I would find it much more straightforward to understand if each async_write_some operation corresponded 1:1 to either a detail::async_write_some_impl op or a TransmitFile op and that the specialization of async_write_some for file_win32 bodies does not do any fancy coalescing of write ops...  
  
What do you think?  
  
Also, is it problematic that the async_write_some specialization overwrites the split state of the serializer? Should we set it back afterwards?

---

## Comment 6

> Username: mika-fischer  
> Created at: 2019-12-23 15:04:43 UTC  
> Url: https://github.com/boostorg/beast/issues/1786#issuecomment-568498537  

#1788 Implements the 1:1 approach, which also reduces the state of the op...

---

## Comment 7

> Username: vinniefalco  
> Created at: 2019-12-23 16:54:05 UTC  
> Updated at: 2019-12-23 16:54:15 UTC  
> Url: https://github.com/boostorg/beast/issues/1786#issuecomment-568525051  

There are problems with my code:  
1. There is no support for other platforms  
2. It is difficult or impossible to test correctly  
3. The _Body_ concept, and the specification of the body reader and body writer are guaranteed to undergo non backward-compatible changes in the Great HTTP Refactor (https://github.com/boostorg/beast/projects/5)  
  
Because of this, I think it is best to touch this stuff as little as possible. I would like to touch it just enough to fix any bugs, and no more. That said, we do have to respect the `split` flag as you have done in the pull request.  
  
> is it problematic that the async_write_some specialization overwrites the split state of the serializer? Should we set it back afterwards?  
  
I don't think it is problematic. Beast HTTP write operation documentation states that a side effect is to change the split state. Since a serializer cannot be reused, setting it back is not necessary.  
  
I'm a little confused, because I don't see where you are checking `split` in your commit??

---

## Comment 8

> Username: mika-fischer  
> Created at: 2019-12-23 18:35:27 UTC  
> Url: https://github.com/boostorg/beast/issues/1786#issuecomment-568548698  

All right, then the patch in the pull request should be fine.  
  
Previously, in the case that the header is not already sent, the code set split to true (it still does this now and that's all that's needed), and calls the normal async_write_some_impl function. In the completion handler, previously, this case was detected *and then the op was restarted/continued* no matter what split was set to before the op started.  
  
My patch changes this, so that the op completes with every completion of async_write_some_impl or TransmitFile.  
  
Previously, the async_write_header op expected async_write_some to return after the header was written, which the old version of the file_win32 specialization did not do.  
  
Hope this clears things up!

---

## Comment 9

> Username: FinAddic  
> Created at: 2020-12-04 21:09:37 UTC  
> Url: https://github.com/boostorg/beast/issues/1786#issuecomment-739022006  

Really glad I found this thread.  Saved me.   
  
Sorry about commenting on a closed issue, but just in case there is something else lurking in there ... I thought I'd add this finding.   
  
I wrote a class that makes use of the file_body type of request, and in my windows test program I actually had no issue with async_write_header, but when I built it into our main system, I found I was running into this exact issue.  
  
Both the test program and full application are built w/ same boost version 1.72 (yes I'm stuck there for awhile).  Both 64-bit WIN32 apps.  So far no clue why it works differently TBH.   
  
I have a workaround by simply using the sync version of write_header (ugly but seems ok). Just added in case this turns on any additional light bulbs.   
  
Thanks for writing this really cool package.
