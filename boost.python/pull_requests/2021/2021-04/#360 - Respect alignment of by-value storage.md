# #360 Respect alignment of by-value storage. [Merged]

> Username: stefanseefeld  
> Created at: 2021-04-26 14:45:44 UTC  
> Updated at: 2021-05-01 02:59:25 UTC  
> Merged at: 2021-05-01 02:59:25 UTC  
> Closed at: 2021-05-01 02:59:25 UTC  
> Url: https://github.com/boostorg/python/pull/360  



---

## Comment 1

> Username: stefanseefeld  
> Created_at: 2021-04-27 13:46:04 UTC  
> Url: https://github.com/boostorg/python/pull/360#issuecomment-827619214  

This is an adaptation of https://github.com/boostorg/python/pull/35, with a few notable changes:  
* include only changes needed to support by-value storage of custom-aligned types.  
* make sure not to break API compatibility (for custom converters, specifically). Notably, don't use `boost::aligned_storage::address()`, which is also not present in `std::aligned_storage`.

---

## Comment 2

> Username: nevion  
> Created_at: 2021-04-27 16:28:15 UTC  
> Updated_at: 2021-04-27 16:55:30 UTC  
> Url: https://github.com/boostorg/python/pull/360#issuecomment-827742813  

I do not think it is safe to use bytes inside of boost.python where objects may be aligned ; the start of user data will differ bytes having an alignment of 1 will  will read before the userdata in the aligned type.  
  
Note that the original layout was done under the assumption and time where it was not clear boost.python would allow dependence on c++11.  However the previous PR incidcated a willingness to use c++11 compiler and that simplifies things, including bytes vs address().  
  
Try the following... I'd PR against your PR but I haven't been able to get a boost build going in a hurry (if you have instructions on doing a minimal boost.python build, esp with cmake that'd be great - bjam from boost superbuild gives me a few different errors trying to get started).  
  
```  
-template <std::size_t size, std::size_t alignment = std::size_t(-1)>  
+template <std::size_t size, std::size_t alignment>  
 struct aligned_storage  
 {  
-  union type  
-  {  
-    typename ::boost::aligned_storage<size, alignment>::type data;  
-    char bytes[size];  
-  };  
+  alignas(alignment) char bytes[size];  
 };  
         
   // Compute the size of T's referent. We wouldn't need this at all,  
@@ -37,7 +33,7 @@ struct aligned_storage  
 template <class T>  
 struct referent_storage  
 {  
-    typedef typename aligned_storage<referent_size<T>::value, alignment_of<T>::value>::type type;  
+    typedef aligned_storage<referent_size<T>::value, alignment_of<T>::value> type;  
 };  
```  
  
It might be worth changing the parameterization of aligned_storage further (trimming the fat) to T to end up with:  
  
```  
-template <std::size_t size, std::size_t alignment = std::size_t(-1)>  
+template <typename T>  
 struct aligned_storage  
 {  
-  union type  
-  {  
-    typename ::boost::aligned_storage<size, alignment>::type data;  
-    char bytes[size];  
-  };  
+  alignas(alignof(T)) char bytes[sizeof(T)];  
 };  
         
-  // Compute the size of T's referent. We wouldn't need this at all,  
-  // but sizeof() is broken in CodeWarriors <= 8.0  
-  template <class T> struct referent_size;  
-    
-    
-  template <class T>  
-  struct referent_size<T&>  
-  {  
-      BOOST_STATIC_CONSTANT(  
-          std::size_t, value = sizeof(T));  
-  };  
-  
 // A metafunction returning a POD type which can store U, where T ==  
 // U&. If T is not a reference type, returns a POD which can store T.  
 template <class T>  
 struct referent_storage  
 {  
-    typedef typename aligned_storage<referent_size<T>::value, alignment_of<T>::value>::type type;  
+    typedef aligned_storage<T> type;  
 };  
```  
  
If prior to c++11 is important, one could alter the definition of aligned_storage to include padding bytes  (e.g. ```char padding[alignment-1]``` )necessary to get it to the right alignment, perhaps using partial template specialization to make it easier wrt zero sized arrays.

---

## Comment 3

> Username: stefanseefeld  
> Created_at: 2021-04-27 18:04:27 UTC  
> Url: https://github.com/boostorg/python/pull/360#issuecomment-827804790  

I may try to look into this tonight when I have more time. But I'm surprised you think we are now assuming a C++11-compliant compiler. I don't think I added any C++11-specific bits into the PR. Did I ? Where ?  
Boost.Python definitely supports C++11 and beyond, but I don't think we have decided to make C++11 support a requirement yet.

---

## Comment 4

> Username: nevion  
> Created_at: 2021-04-27 18:53:31 UTC  
> Url: https://github.com/boostorg/python/pull/360#issuecomment-827839117  

I walked it back a bit when I edited the post, I got the idea you approved of c++11 because std::aligned_storage is c++11 and you wanted to use it.  The updated post gives some other alternatives.

---

## Comment 5

> Username: stefanseefeld  
> Created_at: 2021-04-27 19:03:55 UTC  
> Url: https://github.com/boostorg/python/pull/360#issuecomment-827845604  

I definitely want to be ready for C++11. `boost::aligned_storage` can be used in a way that is API-compatible with `std::aligned_storage`, so that's what I'm doing here (and why I'm not using the boost-specific `address()` member function).  
This allows to write the code as  
```  
#if __cplusplus <= 201103L  
using boost::aligned_storage;  
#else  
using std::aligned_storage;  
#endif  
```  
(which is what the boost/python/details/type_traits.hpp header is doing).  
  
I'm also not sure about your comment concerning the use of `bytes` not being safe. At least since C++14 it is explicitly stated that all union (data) members use the same address. I don't think I have ever seen a compiler (even pre-C++14) where that wasn't the case.

---

## Comment 6

> Username: nevion  
> Created_at: 2021-04-30 20:59:15 UTC  
> Updated_at: 2021-04-30 21:00:24 UTC  
> Url: https://github.com/boostorg/python/pull/360#issuecomment-830387188  

You're right, [c mandates the start of each member to be the same address](http://port70.net/~nsz/c/c11/n1570.html#6.7.2.1p16), and that is the start of the union in memory. The type-with-alignment implementation deduces a type that has the alignment requested,and interestingly can increase the size of the union , but this is not to be confused with padding - with this implementation the union is still at offset 0 in the memory layout.  I suppose that also means the sizeof the union is different from what is needed since this is just alignment hacks, in some cases.  What happens with auto storage variables is they are put on the stack at an address that meets the alignment requirement. So bytes were all that was needed since no padding occurs. .

---

## Comment 7

> Username: stefanseefeld  
> Created_at: 2021-04-30 21:15:32 UTC  
> Url: https://github.com/boostorg/python/pull/360#issuecomment-830400889  

The union will have the same alignment and the same size as the alignment-restricted object within it. (It's that alignment-restricted object that may be bigger to make sure objects stacked in arrays are all correctly aligned.)  
  
So for this PR this means that accessing the `.bytes` member works fine, portably. I'm going to merge this soon. It might be useful to add tests, in particular involving Eigen types. That would be changes only to the CI and test logic, I expect.

---
