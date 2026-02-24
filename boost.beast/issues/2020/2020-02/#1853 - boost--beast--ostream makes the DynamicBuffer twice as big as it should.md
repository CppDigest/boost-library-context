# #1853 - boost::beast::ostream makes the DynamicBuffer twice as big as it should [Closed]

> Username: reddwarf69  
> Created at: 2020-02-24 21:34:06 UTC  
> Updated at: 2020-02-29 22:55:49 UTC  
> Closed at: 2020-02-29 22:32:22 UTC  
> Url: https://github.com/boostorg/beast/issues/1853  

```  
#include <boost/beast/core/flat_buffer.hpp>  
#include <boost/beast/core/ostream.hpp>  
#include <iostream>  
  
int main()  
{  
    boost::beast::flat_buffer buffer;  
    boost::beast::ostream(buffer) << "Hello, world!" << std::endl;  
    std::cout << "string size: " << std::string{"Hello, world!\n"}.size() << std::endl;  
    std::cout << "buffer size: " << buffer.size() << std::endl;  
}  
```  
outputs  
  
```  
string size: 14  
buffer size: 28  
```  
  
If you remove the `std::endl` the problem goes away.  
  
I guess it has to do with not handling the `flush` calls correctly?

---

## Comment 1

> Username: vinniefalco  
> Created at: 2020-02-24 22:02:10 UTC  
> Url: https://github.com/boostorg/beast/issues/1853#issuecomment-590571268  

@madmongo1 need to write a test for this case first

---

## Comment 2

> Username: reddwarf69  
> Created at: 2020-02-24 23:38:22 UTC  
> Url: https://github.com/boostorg/beast/issues/1853#issuecomment-590604249  

This seems to fix the issue:  
```  
diff --git a/include/boost/beast/core/detail/ostream.hpp b/include/boost/beast/core/detail/ostream.hpp  
index 062a82d7..0a9a7e7c 100644  
--- a/include/boost/beast/core/detail/ostream.hpp  
+++ b/include/boost/beast/core/detail/ostream.hpp  
@@ -76,6 +76,18 @@ public:  
             ch, Traits::eof()));  
         sync();  
   
+        BOOST_ASSERT(b_.capacity() > b_.size());  
+        return this->sputc(  
+            Traits::to_char_type(ch));  
+    }  
+  
+    int  
+    sync() override  
+    {  
+        b_.commit(  
+            (this->pptr() - this->pbase()) *  
+            sizeof(CharT));  
+  
         static std::size_t constexpr max_size = 65536;  
         auto const max_prepare = std::min<std::size_t>(  
             std::max<std::size_t>(  
@@ -89,17 +101,6 @@ public:  
         auto const p = static_cast<CharT*>(b.data());  
         this->setp(p, p + b.size() / sizeof(CharT));  
   
-        BOOST_ASSERT(b_.capacity() > b_.size());  
-        return this->sputc(  
-            Traits::to_char_type(ch));  
-    }  
-  
-    int  
-    sync() override  
-    {  
-        b_.commit(  
-            (this->pptr() - this->pbase()) *  
-            sizeof(CharT));  
         return 0;  
     }  
 };  
```  
  
According to the current DynamicBuffer requirements, after calling commit() `All constant or mutable buffer sequences previously obtained using data() or prepare() are invalidated.` so `prepare()` needs to be called in `sync()` anyway to make sure future writes use a valid buffer. When doing so pptr() gets correctly updated so repeated calls to `sync()` don't over-increase the size of the buffer.

---

## Comment 3

> Username: reddwarf69  
> Created at: 2020-02-25 08:16:22 UTC  
> Updated at: 2020-02-25 08:16:45 UTC  
> Url: https://github.com/boostorg/beast/issues/1853#issuecomment-590740211  

But that does a wasted allocation in the destructor, which is not ideal. So I think the correct fix is just this:  
  
```  
diff --git a/include/boost/beast/core/detail/ostream.hpp b/include/boost/beast/core/detail/ostream.hpp  
index 062a82d7..8af7760d 100644  
--- a/include/boost/beast/core/detail/ostream.hpp  
+++ b/include/boost/beast/core/detail/ostream.hpp  
@@ -100,6 +100,7 @@ public:  
         b_.commit(  
             (this->pptr() - this->pbase()) *  
             sizeof(CharT));  
+        this->setp(nullptr, nullptr);  
         return 0;  
     }  
 };  
@@ -169,6 +170,7 @@ public:  
         b_.commit(  
             (this->pptr() - this->pbase()) *  
             sizeof(CharT));  
+        this->setp(nullptr, nullptr);  
         return 0;  
     }  
 };  
```  
Forcing to call `overflow` the next time there is a need to write a character, if that ever happens.

---

## Comment 4

> Username: madmongo1  
> Created at: 2020-02-29 22:55:48 UTC  
> Url: https://github.com/boostorg/beast/issues/1853#issuecomment-593003050  

@RedDwarf69 Your two commits have been merged to develop.  
Thank you for creating (and solving!) the issues.  
Welcome to the team!
