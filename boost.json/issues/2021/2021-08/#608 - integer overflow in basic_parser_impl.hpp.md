# #608 - integer overflow in basic_parser_impl.hpp [Closed]

> Username: pauldreik  
> Created at: 2021-08-16 20:51:38 UTC  
> Updated at: 2021-08-17 19:19:26 UTC  
> Closed at: 2021-08-17 19:19:26 UTC  
> Url: https://github.com/boostorg/json/issues/608  

Sorry for this lousy report, just wanted to document what I found so far before having to stop for the day.   
  
oss fuzz found this integer overflow, https://oss-fuzz.com/testcase-detail/4763163676508160  
  
The input to parse is 9e2147483609  
  
One gets an integer overflow: 2147483600 + 57 cannot be represented in type 'int'  
  
57 is the ascii value for '9'  
  
I think I found the issue, it is https://github.com/boostorg/json/blob/develop/include/boost/json/basic_parser_impl.hpp#L2366 carrying out the "+ c" before subtracting '0'.  
  
I believe this might fix it:  
```diff  
diff --git a/include/boost/json/basic_parser_impl.hpp b/include/boost/json/basic_parser_impl.hpp  
index dc926c9..29ba52e 100644  
--- a/include/boost/json/basic_parser_impl.hpp  
+++ b/include/boost/json/basic_parser_impl.hpp  
@@ -2363,7 +2363,7 @@ do_exp3:  
                     num.exp == 214748364 && c > '7')))  
                 return fail(cs.begin(), error::exponent_overflow);  
             ++cs;  
-            num.exp = 10 * num.exp + c - '0';  
+            num.exp = 10 * num.exp + ( c - '0' );  
             continue;  
         }  
         goto finish_dub;  
```

---

## Comment 1

> Username: grisumbras  
> Created at: 2021-08-17 19:19:26 UTC  
> Url: https://github.com/boostorg/json/issues/608#issuecomment-900566747  

Fixed in #609
