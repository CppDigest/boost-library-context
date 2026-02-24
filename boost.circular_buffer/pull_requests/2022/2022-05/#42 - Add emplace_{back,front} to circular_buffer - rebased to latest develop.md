# #42 Add emplace_{back,front} to circular_buffer - rebased to latest develop [Open]

> Username: f9z  
> Created at: 2022-05-13 02:38:21 UTC  
> Updated at: 2023-06-02 17:58:07 UTC  
> Url: https://github.com/boostorg/circular_buffer/pull/42  

based on https://github.com/boostorg/circular_buffer/pull/15 request by [AI0867](https://github.com/boostorg/circular_buffer/commits?author=AI0867)

---

## Comment 1

> Username: f9z  
> Created_at: 2022-05-13 02:52:16 UTC  
> Url: https://github.com/boostorg/circular_buffer/pull/42#issuecomment-1125605713  

@AI0867 i've taken your code rebased to develop and made minor adjustments to be compatible with latest changes.  
@gpascualg would highly appreciate if you could help me with review please. i used your proposed patch to avoid extra destructor call.

---

## Comment 2

> Username: AI0867  
> Created_at: 2022-05-16 07:43:42 UTC  
> Url: https://github.com/boostorg/circular_buffer/pull/42#issuecomment-1127336558  

@f9z Thank you for your efforts. I haven't had the time to set up a build environment on my current machine to do this myself.

---

## Comment 3

> Username: gpascualg  
> Created_at: 2022-05-16 09:10:41 UTC  
> Url: https://github.com/boostorg/circular_buffer/pull/42#issuecomment-1127422143  

Looks good to me, plus it's more consistent with the circular_buffer code than the patch I proposed.  
Thank you for putting it together.

---

## Comment 4

> Username: f9z  
> Created_at: 2022-05-16 11:46:17 UTC  
> Url: https://github.com/boostorg/circular_buffer/pull/42#issuecomment-1127569632  

That'd be the procedure to merge it to "develop" from here @gpascualg ? It's my first PR to boostorg. Anything else I need to do to make it happen? Thanks!

---

## Comment 5

> Username: gpascualg  
> Created_at: 2022-05-16 12:21:35 UTC  
> Url: https://github.com/boostorg/circular_buffer/pull/42#issuecomment-1127604188  

I'm afraid I can't really help you here, as I have never done a PR to any boost library. I'd guess, though, that this should be it.

---

## Comment 6

> Username: f9z  
> Created_at: 2022-05-17 04:41:51 UTC  
> Url: https://github.com/boostorg/circular_buffer/pull/42#issuecomment-1128404501  

@glenfe would it be possible to consider merging this PR? please let me know if i'd need to do anything else or approach some other boostorg maintainers. thanks!

---

## Comment 7

> Username: lano1106  
> Created_at: 2023-06-01 18:00:27 UTC  
> Updated_at: 2023-06-01 18:07:47 UTC  
> Url: https://github.com/boostorg/circular_buffer/pull/42#issuecomment-1572539577  

what was the problem with the initial replace() code?  
  
I left a comment about what I feel was observed here https://github.com/boostorg/circular_buffer/pull/15#issuecomment-1572194345  
  
this is an excellent PR but I would sort out the situation and possibly discard the last commit from the PR...

---

## Review 8 [Commented]

> Username: lano1106  
> Created_at: 2023-06-02 16:59:52 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/circular_buffer/pull/42#pullrequestreview-1458043013  

📁 include/boost/circular_buffer/base.hpp

```diff
1478 |-             replace(m_last, value_type(::boost::forward<V>(value)));
1479 |+             destroy_item(m_last);
1480 |+             boost::allocator_construct(alloc(), boost::to_address(m_last), ::boost::forward<Args>(args)...);
```

> Username: lano1106  
> Created_at: 2023-06-02 16:59:51 UTC  
> Updated_at: 2023-06-02 16:59:52 UTC  
> Url: https://github.com/boostorg/circular_buffer/pull/42#discussion_r1214611440  

This has not been compiled with BOOST_NO_CXX11_VARIADIC_TEMPLATES defined because I do not think this would compile.  
  
There is no variadic template parameter Args (args) here


---

## Review 9 [Commented]

> Username: lano1106  
> Created_at: 2023-06-02 17:00:21 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/circular_buffer/pull/42#pullrequestreview-1458044261  

📁 include/boost/circular_buffer/base.hpp

```diff
1518 |-                 replace(m_first, value_type(::boost::forward<V>(value)));
1521 |+                 destroy_item(m_first);
1522 |+                 boost::allocator_construct(alloc(), boost::to_address(m_first), ::boost::forward<Args>(args)...);
```

> Username: lano1106  
> Created_at: 2023-06-02 17:00:21 UTC  
> Url: https://github.com/boostorg/circular_buffer/pull/42#discussion_r1214612085  

This has not been compiled with BOOST_NO_CXX11_VARIADIC_TEMPLATES defined because I do not think this would compile.  
  
There is no variadic template parameter Args (args) here


---

## Review 10 [Commented]

> Username: lano1106  
> Created_at: 2023-06-02 17:21:09 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/circular_buffer/pull/42#pullrequestreview-1458092724  

📁 include/boost/circular_buffer/base.hpp

```diff
1461 |             if (empty())
1462 |                 return;
1463 |-             replace(m_last, value_type(::boost::forward<Args>(args)...));
```

> Username: lano1106  
> Created_at: 2023-06-02 17:21:08 UTC  
> Updated_at: 2023-06-02 17:21:09 UTC  
> Url: https://github.com/boostorg/circular_buffer/pull/42#discussion_r1214630511  

this line is wrong. This is your bug!  
  
This is where the temporary object gets created.  
  
What do you think value_type(...) is doing?  
  
It creates a temporary object and call the replace() method that is using assignment!


---

## Comment 11

> Username: lano1106  
> Created_at: 2023-06-02 17:56:12 UTC  
> Updated_at: 2023-06-02 17:58:07 UTC  
> Url: https://github.com/boostorg/circular_buffer/pull/42#issuecomment-1574109051  

Here is my take. This is a patch over branch after having reverted "replace replace() with destroy_item() + construct()"  
      
This reverts commit c4c85d730dc8e31c4d50aa45ada146ce8c2f5340.  
  
```  
diff --git a/include/boost/circular_buffer/base.hpp b/include/boost/circular_buffer/base.hpp  
index 5ed1321..846cb32 100644  
--- a/include/boost/circular_buffer/base.hpp  
+++ b/include/boost/circular_buffer/base.hpp  
@@ -1460,7 +1460,7 @@ private:  
         if (full()) {  
             if (empty())  
                 return;  
-            replace(m_last, value_type(::boost::forward<Args>(args)...));  
+            destructive_replace(m_last, ::boost::forward<Args>(args)...);  
             increment(m_last);  
             m_first = m_last;  
         } else {  
@@ -1470,16 +1470,16 @@ private:  
         }  
     }  
 #else  
-    template <class V>  
-    void emplace_back_impl(BOOST_FWD_REF(V) value) {  
+    template <class Arg>  
+    void emplace_back_impl(BOOST_FWD_REF(Arg) arg) {  
         if (full()) {  
             if (empty())  
                 return;  
-            replace(m_last, value_type(::boost::forward<V>(value)));  
+            destructive_replace(m_last, ::boost::forward<Arg>(arg));  
             increment(m_last);  
             m_first = m_last;  
         } else {  
-            boost::allocator_construct(alloc(), boost::to_address(m_last), ::boost::forward<V>(value));  
+            boost::allocator_construct(alloc(), boost::to_address(m_last), ::boost::forward<Arg>(arg));  
             increment(m_last);  
             ++m_size;  
         }  
@@ -1494,7 +1494,7 @@ private:  
                 if (empty())  
                     return;  
                 decrement(m_first);  
-                replace(m_first, value_type(::boost::forward<Args>(args)...));  
+                destructive_replace(m_first, ::boost::forward<Args>(args)...);  
                 m_last = m_first;  
             } else {  
                 decrement(m_first);  
@@ -1508,18 +1508,18 @@ private:  
         BOOST_CATCH_END  
     }  
 #else  
-    template <class V>  
-    void emplace_front_impl(BOOST_FWD_REF(V) value) {  
+    template <class Arg>  
+    void emplace_front_impl(BOOST_FWD_REF(Arg) arg) {  
         BOOST_TRY {  
             if (full()) {  
                 if (empty())  
                     return;  
                 decrement(m_first);  
-                replace(m_first, value_type(::boost::forward<V>(value)));  
+                destructive_replace(m_first, ::boost::forward<Arg>(arg));  
                 m_last = m_first;  
             } else {  
                 decrement(m_first);  
-                boost::allocator_construct(alloc(), boost::to_address(m_first), ::boost::forward<V>(value));  
+                boost::allocator_construct(alloc(), boost::to_address(m_first), ::boost::forward<Arg>(arg));  
                 ++m_size;  
             }  
         } BOOST_CATCH(...) {  
@@ -2560,6 +2560,30 @@ private:  
 #endif  
     }  
   
+#if !defined(BOOST_NO_CXX11_VARIADIC_TEMPLATES)  
+    template<class... Args>  
+    void destructive_replace(pointer pos, BOOST_FWD_REF(Args)... args)  
+    {  
+        boost::allocator_destroy(alloc(), boost::to_address(pos));  
+        boost::allocator_construct(alloc(), boost::to_address(pos),  
+                                   ::boost::forward<Args>(args)...);  
+#if BOOST_CB_ENABLE_DEBUG  
+        invalidate_iterators(iterator(this, pos));  
+#endif  
+    }  
+#else  
+    template <class Arg>  
+    void destructive_replace(pointer pos, BOOST_FWD_REF(Arg) arg)  
+    {  
+        boost::allocator_destroy(alloc(), boost::to_address(pos));  
+        boost::allocator_construct(alloc(), boost::to_address(pos),  
+                                   ::boost::forward<Arg>(arg));  
+#if BOOST_CB_ENABLE_DEBUG  
+        invalidate_iterators(iterator(this, pos));  
+#endif  
+    }  
+#endif  
+  
     /*! INTERNAL ONLY */  
     void construct_or_replace(bool construct, pointer pos, param_value_type item) {  
         if (construct)  
```

---
