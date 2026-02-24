# #107 - C++03 bugs in `allocator_construct` [Closed]

> Username: cmazakas  
> Created at: 2022-03-14 22:42:51 UTC  
> Updated at: 2022-03-16 00:09:51 UTC  
> Closed at: 2022-03-15 20:05:37 UTC  
> Url: https://github.com/boostorg/core/issues/107  

While refactoring the Unordered codebase, I unfortunately ran into some problems with Core's Allocator Access implementation in C++03 mode only.  
  
Unordered has a special test allocator implementation that manages to track calls to both `construct()` and `destroy()` ensuring that they match up perfectly, raising a failed test case otherwise.  
  
It seems that in C++03 mode, that `allocator_construct` does not correctly go through member function `construct()` in all cases.  
  
The Allocator implements `construct()` like this:  
```cpp  
void construct(T* p) const  
{  
  detail::tracker.track_construct((void*)p, sizeof(T), tag_);  
  new (p) T();  
}  
  
template <class U>  
void construct(T* p, U const& u) const  
{  
  detail::tracker.track_construct((void*)p, sizeof(T), tag_);  
  new (p) T(u);  
}  
```  
  
Originally, this test allocator was missing the single argument overload which is indeed incorrect as was fixed on the Unordered side.  
  
Unfortunately, when constructing nodes to insert into the table:  
```cpp  
v2_node_pointer nptr = boost::allocator_allocate(node_allocator, 1);  
boost::allocator_construct(  
  node_allocator, boost::to_address(nptr), *i);  
```  
this was simply calling `::new(p) T(t)` instead of going through the member function construct above.  
  
I managed to fix it using this diff:  
```  
diff --git a/include/boost/core/allocator_access.hpp b/include/boost/core/allocator_access.hpp  
index 7717ba0..af4cf97 100644  
--- a/include/boost/core/allocator_access.hpp  
+++ b/include/boost/core/allocator_access.hpp  
@@ -515,15 +515,34 @@ allocator_construct(A&, T* p, V&& v)  
 }  
 #endif  
 #else  
+  
 template<class A, class T, class V>  
-inline void  
+inline typename detail::alloc_if<detail::alloc_has_construct<A,  
+    T>::value>::type  
+allocator_construct(A& a, T* p, const V& v)  
+{  
+    a.construct(p, v);  
+}  
+  
+template<class A, class T, class V>  
+inline typename detail::alloc_if<detail::alloc_has_construct<A,  
+    T>::value>::type  
+allocator_construct(A& a, T* p, V& v)  
+{  
+    a.construct(p, v);  
+}  
+  
+template<class A, class T, class V>  
+inline typename detail::alloc_if<!detail::alloc_has_construct<A,  
+    T>::value>::type  
 allocator_construct(A&, T* p, const V& v)  
 {  
     ::new((void*)p) T(v);  
 }  
  
 template<class A, class T, class V>  
-inline void  
+inline typename detail::alloc_if<!detail::alloc_has_construct<A,  
+    T>::value>::type  
 allocator_construct(A&, T* p, V& v)  
 {  
     ::new((void*)p) T(v);  
```

---

## Comment 1

> Username: glenfe  
> Created at: 2022-03-15 15:01:41 UTC  
> Updated at: 2022-03-15 15:02:27 UTC  
> Url: https://github.com/boostorg/core/issues/107#issuecomment-1068089613  

I introduced this in `develop` three days ago as part of deprecating `alloc_construct` and consequently making `allocator_construct` support `noinit_adaptor` in C++03 (by detecting no-argument construct and destroy in C++03 rather than overloading for `noinit_adaptor`)   
  
The proposed diff is wrong, because `alloc_has_construct` only checks for no-argument construct, not single argument construct.   
  
I'll fix it tonight.

---

## Comment 2

> Username: glenfe  
> Created at: 2022-03-15 20:05:37 UTC  
> Url: https://github.com/boostorg/core/issues/107#issuecomment-1068417956  

Should be addressed by 5e0ff1680f696868add3ca8d614c095475fce454

---

## Comment 3

> Username: pdimov  
> Created at: 2022-03-15 23:55:14 UTC  
> Url: https://github.com/boostorg/core/issues/107#issuecomment-1068578845  

That's not going to fix it though; allocator_construct will not go through Allocator::construct in C++03 mode. Unless I'm missing something.

---

## Comment 4

> Username: glenfe  
> Created at: 2022-03-16 00:02:44 UTC  
> Updated at: 2022-03-16 00:09:51 UTC  
> Url: https://github.com/boostorg/core/issues/107#issuecomment-1068583299  

It wasn't going through Allocator::construct before, and Unordered was fine. My recent change that broke things:  
* Made allocator_construct(a, p) go through Allocator::construct (when detected)  in C++03  
* Made allocator_destroy(a, p) go through Allocator::destroy (when detected) in C++03    
  
But allocator_construct(a, p, v) was still going through placement new in C++03 so there was a mismatch:  
* The Unordered test's allocator's destroy() was being called in C++03, but its construct(p, v) was not.   
  
I reverted to the old behavior (where all allocator_constructs  in C++03 go through placement new, and all allocator_destroys in C++03 just call the destructor).
