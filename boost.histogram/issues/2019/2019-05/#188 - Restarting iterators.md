# #188 - Restarting iterators [Closed]

> Username: henryiii  
> Created at: 2019-05-01 18:55:07 UTC  
> Updated at: 2019-05-04 07:27:45 UTC  
> Closed at: 2019-05-04 07:26:47 UTC  
> Url: https://github.com/boostorg/histogram/issues/188  

The following does not work as expected:  
  
```cpp  
auto ind = indexed(h);  
for(auto&& v : ind)  
   ...  
for(auto&& v : ind)  
   // Only the first dimension will restart, the other dimensiond are stuck at the end, at least for bins.  
```  
  
It should either work or do something like throwing an exception.  
  
A minor detail about the iterator `indexted()` produces that might not warrant it's own issue: it would be nice to have easy access to the `.rank` of the histogram so that one can tell how many values `.bin` and `.index` take. Edit: `it->indices().size()` is a little wasteful, but it is a pretty good workaround.  
  
---  
  
If giving a valid validator whenever `.begin()` is called can be implemented, the following would be the change to the indexed_test.cpp:  
  
```patch  
diff --git a/test/indexed_test.cpp b/test/indexed_test.cpp  
index 1f9bf52..eb754bb 100644  
--- a/test/indexed_test.cpp  
+++ b/test/indexed_test.cpp  
@@ -77,25 +77,29 @@ void run_3d_tests(mp_list<IsDynamic, Coverage>) {  
       for (int k = -1; k < 5; ++k) h(i, j, k, weight(i * 100 + j * 10 + k));  
  
   auto ind = indexed(h, Coverage());  
-  auto it = ind.begin();  
-  BOOST_TEST_EQ(it->indices().size(), 3);  
-  
-  const int d = Coverage() == coverage::all;  
+  // Verify this works when restarted  
+  for(int ii=0; ii<2; ii++) {  
+    auto it = ind.begin();  
+    BOOST_TEST_EQ(it->indices().size(), 3);  
+  
+    const int d = Coverage() == coverage::all;  
+  
+    // imitate iteration order of indexed loop  
+    for (int k = 0; k < 4 + d; ++k)  
+      for (int j = 0; j < 3; ++j)  
+        for (int i = -d; i < 2 + d; ++i) {  
+          BOOST_TEST_EQ(it->index(0), i);  
+          BOOST_TEST_EQ(it->index(1), j);  
+          BOOST_TEST_EQ(it->index(2), k);  
+          BOOST_TEST_EQ(it->bin(0_c), h.axis(0_c).bin(i));  
+          BOOST_TEST_EQ(it->bin(1_c), h.axis(1_c).bin(j));  
+          BOOST_TEST_EQ(it->bin(2_c), h.axis(2_c).bin(k));  
+          BOOST_TEST_EQ(**it, i * 100 + j * 10 + k);  
+          ++it;  
+        }  
+    BOOST_TEST(it == ind.end());  
  
-  // imitate iteration order of indexed loop  
-  for (int k = 0; k < 4 + d; ++k)  
-    for (int j = 0; j < 3; ++j)  
-      for (int i = -d; i < 2 + d; ++i) {  
-        BOOST_TEST_EQ(it->index(0), i);  
-        BOOST_TEST_EQ(it->index(1), j);  
-        BOOST_TEST_EQ(it->index(2), k);  
-        BOOST_TEST_EQ(it->bin(0_c), h.axis(0_c).bin(i));  
-        BOOST_TEST_EQ(it->bin(1_c), h.axis(1_c).bin(j));  
-        BOOST_TEST_EQ(it->bin(2_c), h.axis(2_c).bin(k));  
-        BOOST_TEST_EQ(**it, i * 100 + j * 10 + k);  
-        ++it;  
-      }  
-  BOOST_TEST(it == ind.end());  
+  }  
 }  
  
 template <class IsDynamic, class Coverage  
```

---

## Comment 1

> Username: HDembinski  
> Created at: 2019-05-04 07:26:47 UTC  
> Url: https://github.com/boostorg/histogram/issues/188#issuecomment-489303089  

You cannot restart the indexed_range because it is Forward Range. To do its job, indexed_range uses a workspace whose life-time is bound to its life-time. It is efficient generate a new indexed_range for each loop since it allocates its workspace on the stack. In other words, there is no need to be "clever" and reuse an instance. Boost.Histogram is written so that you do not have to be clever. If you do what the documentation says, you already get the most efficient code.

---

## Comment 2

> Username: HDembinski  
> Created at: 2019-05-04 07:27:45 UTC  
> Url: https://github.com/boostorg/histogram/issues/188#issuecomment-489303141  

The behavior of calling begin several times was changed from "undefined" to "defined and documented" in the develop branch.
