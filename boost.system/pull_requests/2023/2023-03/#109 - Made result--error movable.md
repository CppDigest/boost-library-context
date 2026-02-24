# #109 Made result::error movable. [Merged]

> Username: klemens-morgenstern  
> Created at: 2023-03-08 00:20:18 UTC  
> Updated at: 2023-03-18 05:17:28 UTC  
> Merged at: 2023-03-18 05:17:28 UTC  
> Closed at: 2023-03-18 05:17:28 UTC  
> Url: https://github.com/boostorg/system/pull/109  

Closes #108.

---

## Comment 1

> Username: pdimov  
> Created_at: 2023-03-09 02:46:59 UTC  
> Url: https://github.com/boostorg/system/pull/109#issuecomment-1461186875  

I don't see why you seem to have made `XM` copyable. The test is supposed to test a move-only type.

---

## Review 2 [Commented]

> Username: klemens-morgenstern  
> Created_at: 2023-03-09 10:22:48 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/system/pull/109#pullrequestreview-1332433861  

📁 test/result_error_move.cpp

```diff
  16 |+     explicit XM( int v = 0 ): v_( v ) {}
  17 |+ 
  18 |+     XM( XM const& ) = default;
```

> Username: klemens-morgenstern  
> Created_at: 2023-03-09 10:22:48 UTC  
> Url: https://github.com/boostorg/system/pull/109#discussion_r1130776976  

```diff  
-    XM( XM const& ) = default;  
+    XM( XM const& ) = delete;  
```  
  
This should've been `delete`.


---

## Comment 3

> Username: pdimov  
> Created_at: 2023-03-09 12:49:56 UTC  
> Url: https://github.com/boostorg/system/pull/109#issuecomment-1462006597  

You can't move from const objects. These tests only worked because of the copy constructor.

---

## Comment 4

> Username: klemens-morgenstern  
> Created_at: 2023-03-16 03:37:16 UTC  
> Url: https://github.com/boostorg/system/pull/109#issuecomment-1471249253  

yeah, i missed the right-`const`.

---

## Review 5 [Commented]

> Username: pdimov  
> Created_at: 2023-03-16 05:50:36 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/system/pull/109#pullrequestreview-1342956894  

📁 test/CMakeLists.txt

```diff
 164 | boost_test(TYPE run SOURCES result_value_construct4.cpp)
 165 | boost_test(TYPE run SOURCES result_value_construct5.cpp)
 166 |+ boost_test(TYPE run SOURCES result_error_move.cpp)
```

> Username: pdimov  
> Created_at: 2023-03-16 05:50:35 UTC  
> Updated_at: 2023-03-16 05:50:36 UTC  
> Url: https://github.com/boostorg/system/pull/109#discussion_r1138158695  

A newline is missing here at the end of file.


---
