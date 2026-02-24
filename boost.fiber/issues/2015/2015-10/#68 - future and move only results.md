# #68 - future and move only results [Closed]

> Username: dvd0101  
> Created at: 2015-10-11 21:45:51 UTC  
> Updated at: 2015-10-14 19:05:43 UTC  
> Closed at: 2015-10-12 18:45:26 UTC  
> Url: https://github.com/boostorg/fiber/issues/68  

It's possible to use a future to return a move-only object?  
  
This code doesn't compile (gcc 4.9.2 / clang 3.6) (fibers master branch)  
  
```  
#include <boost/fiber/all.hpp>  
#include <iostream>  
  
struct A {  
    A() = default;  
    A(A const&) = delete;  
    A(A&&) = default;  
  
    int value;  
};  
  
int main() {  
    using namespace boost::fibers;  
  
    promise<A> p;  
    auto future = p.get_future();  
    std::cout << future.get().value << std::endl;  
}  
```  
  
gcc output (trimmed):  
  
```  
./support/deps/include/boost/fiber/future/future.hpp: In instantiation of ‘R boost::fibers::future<R>::get() [with R = A]’:  
tests/c.cpp:17:29:   required from here  
./support/deps/include/boost/fiber/future/future.hpp:80:25: error: use of deleted function ‘A::A(const A&)’  
         return tmp->get();  
                         ^  
tests/c.cpp:6:5: note: declared here  
     A(A const&) = delete;  
```

---

## Comment 1

> Username: BenPope  
> Created at: 2015-10-12 01:43:15 UTC  
> Url: https://github.com/boostorg/fiber/issues/68#issuecomment-147272487  

I don't know if this is the preferred way, but I have made the attached patch for this reason.  
  
```  
Index: boost/fiber/future/future.hpp  
===================================================================  
--- boost/fiber/future/future.hpp   (revision 4246)  
+++ boost/fiber/future/future.hpp   (revision 4247)  
@@ -152,7 +152,7 @@  
  
     shared_future< R & > share();  
  
-    R & get() {  
+    R get() {  
         if ( ! valid() ) {  
             throw future_uninitialized();  
         }  
Index: boost/fiber/future/detail/shared_state.hpp  
===================================================================  
--- boost/fiber/future/detail/shared_state.hpp  (revision 4246)  
+++ boost/fiber/future/detail/shared_state.hpp  (revision 4247)  
@@ -81,12 +81,12 @@  
         mark_ready_and_notify_( lk);  
     }  
  
-    R const& get_( std::unique_lock< mutex > & lk) {  
+    R get_( std::unique_lock< mutex > & lk) {  
         wait_( lk);  
         if ( except_) {  
             std::rethrow_exception( except_);  
         }  
-        return value_.get();  
+        return std::move(value_.get());  
     }  
  
     std::exception_ptr get_exception_ptr_( std::unique_lock< mutex > & lk) {  
@@ -161,7 +161,7 @@  
         set_exception_( except, lk);  
     }  
  
-    R const& get() {  
+    R get() {  
         std::unique_lock< mutex > lk( mtx_);  
         return get_( lk);  
     }  
```

---

## Comment 2

> Username: olk  
> Created at: 2015-10-12 18:45:26 UTC  
> Url: https://github.com/boostorg/fiber/issues/68#issuecomment-147488717  

unfortunately it is not fixed yet  
the C++ standard requires signature R & future< R & >::get()

---

## Comment 3

> Username: dvd0101  
> Created at: 2015-10-12 19:26:14 UTC  
> Url: https://github.com/boostorg/fiber/issues/68#issuecomment-147497147  

Can you help me understand?  
  
This code compiles fine:  
  
```  
#include <iostream>  
#include <future>  
  
struct A {  
    A() = default;  
    A(A const&) = delete;  
    A(A&&) = default;  
  
    int value;  
};  
  
int main() {  
    using namespace std;  
  
    promise<A> p;  
    auto future = p.get_future();  
    std::cout << future.get().value << std::endl;  
}  
```  
  
So I don't understand your comment about the C++ standard

---

## Comment 4

> Username: olk  
> Created at: 2015-10-12 20:12:17 UTC  
> Url: https://github.com/boostorg/fiber/issues/68#issuecomment-147506998  

my comment was related to the comment of BenPope

---

## Comment 5

> Username: BenPope  
> Created at: 2015-10-13 00:34:29 UTC  
> Updated at: 2015-10-13 03:34:34 UTC  
> Url: https://github.com/boostorg/fiber/issues/68#issuecomment-147557612  

The standard (§ 30.6.6.13) requires:  
  
```  
R future<R>::get();  
R& future<R&>::get();  
void future<void>::get();  
```  
  
I believe I corrected the signature for future< R >, leaving future< R& > alone.  
  
Update: I was wrong, looks like I messed up the patch to future.hpp, but the one to shared_state still seems valid.  If I make a pull request, against which branch would you prefer?

---

## Comment 6

> Username: dvd0101  
> Created at: 2015-10-14 17:41:41 UTC  
> Url: https://github.com/boostorg/fiber/issues/68#issuecomment-148130888  

What do you think about this patch? It introduces a small asymmetry in the  
implementations of future::get and shared_future::get but the observable  
behaviour should be preserved.  
  
```  
diff -u -r ./detail/shared_state.hpp ../../../../../support/deps/include/boost/fiber/future/detail/shared_state.hpp  
--- ./detail/shared_state.hpp   2015-10-14 19:17:13.169202093 +0200  
+++ ../../../../../support/deps/include/boost/fiber/future/detail/shared_state.hpp  2015-10-14 19:28:04.844428815 +0200  
@@ -89,6 +89,14 @@  
         return value_.get();  
     }  
  
+    R detach_( std::unique_lock< mutex > & lk) {  
+        wait_( lk);  
+        if ( except_) {  
+            std::rethrow_exception( except_);  
+        }  
+        return std::move(value_.get());  
+    }  
+  
     std::exception_ptr get_exception_ptr_( std::unique_lock< mutex > & lk) {  
         wait_( lk);  
         return except_;  
@@ -166,6 +174,11 @@  
         return get_( lk);  
     }  
  
+    R detach() {  
+        std::unique_lock< mutex > lk( mtx_);  
+        return detach_( lk);  
+    }  
+  
     std::exception_ptr get_exception_ptr() {  
         std::unique_lock< mutex > lk( mtx_);  
         return get_exception_ptr_( lk);  
diff -u -r ./future.hpp ../../../../../support/deps/include/boost/fiber/future/future.hpp  
--- ./future.hpp    2015-10-14 19:17:13.169202093 +0200  
+++ ../../../../../support/deps/include/boost/fiber/future/future.hpp   2015-10-14 19:26:14.656559563 +0200  
@@ -77,7 +77,7 @@  
         }  
         ptr_t tmp;  
         tmp.swap( state_);  
-        return tmp->get();  
+        return tmp->detach();  
     }  
  
     std::exception_ptr get_exception_ptr() {  
```  
  
BTW: there is a reason why this issue is closed? We should continue elsewhere?

---

## Comment 7

> Username: olk  
> Created at: 2015-10-14 19:05:43 UTC  
> Url: https://github.com/boostorg/fiber/issues/68#issuecomment-148159331  

please note that boost.fiber is still in development ...  
I've pushed a new version to branch develop - futures support moveable-only objects now
