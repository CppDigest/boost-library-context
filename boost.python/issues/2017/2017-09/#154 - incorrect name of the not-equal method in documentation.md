# #154 - incorrect name of the not-equal method in documentation [Closed]

> Username: pavoljuhas  
> Created at: 2017-09-06 21:41:39 UTC  
> Updated at: 2018-02-20 21:45:07 UTC  
> Closed at: 2018-02-20 21:45:06 UTC  
> Url: https://github.com/boostorg/python/issues/154  

The [reference manual](http://www.boost.org/doc/libs/1_65_0/libs/python/doc/html/reference/high_level_components/boost_python_operators_hpp.html#high_level_components.boost_python_operators_hpp.class_self_ns_self_t.self_t_comparison_functions) shows Python not-equal method as `__nq__` rather than `__ne__`.  The patch below should fix that.  
  
```patch  
From 2b57e2ad4f038c4f4a5ab23b392cf3f09a2cf86c Mon Sep 17 00:00:00 2001  
From: Pavol Juhas <pavol.juhas@gmail.com>  
Date: Wed, 6 Sep 2017 17:04:33 -0400  
Subject: Fix doc typo for the Python `not-equal` method.  
  
---  
 doc/reference/operators.qbk | 4 ++--  
 1 file changed, 2 insertions(+), 2 deletions(-)  
  
diff --git a/doc/reference/operators.qbk b/doc/reference/operators.qbk  
index 892d33b7..8276f681 100644  
--- a/doc/reference/operators.qbk  
+++ b/doc/reference/operators.qbk  
@@ -86,8 +86,8 @@ The column of Python Expressions illustrates the expressions that will be suppor  
 [[C++ Expression][Python Method Name][C++ Implementation][Python Expression (primary, secondary)]]  
 [[`self == r`][`__eq__`][`x == y`][`x == y`, `y == x`]]  
 [[`l == self`][`__eq__`][`y == x`][`y == x`, `x == y`]]  
-[[`self != r`][`__nq__`][`x != y`][`x != y`, `y != x`]]  
-[[`l != self`][`__nq__`][`y != x`][`y != x`, `x != y`]]  
+[[`self != r`][`__ne__`][`x != y`][`x != y`, `y != x`]]  
+[[`l != self`][`__ne__`][`y != x`][`y != x`, `x != y`]]  
 [[`self < r`][`__lt__`][`x < y`][`x < y`, `y > x`]]  
 [[`l < self`][`__gt__`][`y < x`][`y > x`, `x < y`]]  
 [[`self > r`][`__gt__`][`x > y`][`x > y`, `y < x`]]  
--   
2.14.1  
```

---

## Comment 1

> Username: stefanseefeld  
> Created at: 2018-02-20 21:45:06 UTC  
> Url: https://github.com/boostorg/python/issues/154#issuecomment-367130787  

Thanks for the report. Fixed with https://github.com/boostorg/python/commit/2f6e728de5c394f2b26c802b0388cf3f757c84fa
