# #161 - BOOST_WAVE_THROW_NAME_CTX does not result in a context callback and always throws [Closed]

> Username: abakhirkin  
> Created at: 2022-04-12 19:48:52 UTC  
> Updated at: 2022-06-22 18:24:14 UTC  
> Closed at: 2022-06-22 18:24:13 UTC  
> Url: https://github.com/boostorg/wave/issues/161  

Not all context-related exceptions produce a call to `throw_exception()` hook. This is the case for exceptions reported via `BOOST_WAVE_THROW_NAME_CTX`, and in particular, the `illegal_redefinition` warning, when attempting to add to a context a pre-defined macro. Not receiving a callback in response to `illegal_redefinition` and possibly other warnings and remarks means we cannot configure context to continue preprocessing when they occur.  
  
I tried the following patch and it seems to fix this particular issue.  
```  
diff --git a/include/boost/wave/cpp_throw.hpp b/include/boost/wave/cpp_throw.hpp  
index 824b29c..f146419 100644  
--- a/include/boost/wave/cpp_throw.hpp  
+++ b/include/boost/wave/cpp_throw.hpp  
@@ -165,7 +165,7 @@ namespace boost { namespace wave { namespace util  
 // helper macro for throwing exceptions with additional parameter  
 #if !defined(BOOST_WAVE_THROW_NAME_CTX)  
 #define BOOST_WAVE_THROW_NAME_CTX(ctx, cls, code, msg, act_pos, name)         \  
-    boost::wave::util::throw_<cls>(cls::code, msg, act_pos, name)             \  
+    boost::wave::util::throw_<cls>(ctx, cls::code, msg, act_pos, name)        \  
     /**/  
 #endif // BOOST_WAVE_THROW_NAME_CTX  
   
  
```

---

## Comment 1

> Username: jefftrull  
> Created at: 2022-04-12 21:16:28 UTC  
> Url: https://github.com/boostorg/wave/issues/161#issuecomment-1097227038  

Are you telling me we have reversed the order of two arguments? :grimacing:

---

## Comment 2

> Username: abakhirkin  
> Created at: 2022-04-12 21:20:07 UTC  
> Url: https://github.com/boostorg/wave/issues/161#issuecomment-1097230404  

No, I believe `ctx` is missing as the first argument to `throw_()`. Assuming I understand correctly how `THROW_..._CTX` macros should work.

---

## Comment 3

> Username: jefftrull  
> Created at: 2022-04-12 21:26:03 UTC  
> Url: https://github.com/boostorg/wave/issues/161#issuecomment-1097235642  

Ah thanks, yes. I wonder how that ever passed :laughing: at least it's an easy fix. I'll probably tackle this one first as time permits. We just missed the 1.79 release window, sadly.

---

## Comment 4

> Username: jefftrull  
> Created at: 2022-06-22 18:24:13 UTC  
> Url: https://github.com/boostorg/wave/issues/161#issuecomment-1163464082  

Fixed in develop (along with your other issue) - give it a try and reopen if you still have trouble.
