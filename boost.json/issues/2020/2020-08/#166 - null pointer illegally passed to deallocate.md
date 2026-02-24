# #166 - null pointer illegally passed to deallocate [Closed]

> Username: pauldreik  
> Created at: 2020-08-18 09:36:54 UTC  
> Updated at: 2020-08-18 21:37:03 UTC  
> Closed at: 2020-08-18 21:37:03 UTC  
> Url: https://github.com/boostorg/json/issues/166  

The following code (straight from the documentation):  
  
```cpp  
error_code ec;  
value jv = parse( "[1,2,3,4,5]", ec );  
```  
compiled with undefined behaviour sanitizer, causes the following output:  
  
> ../include/boost/json/detail/impl/stack.ipp:38:21: runtime error: null pointer passed as argument 1, which is declared to never be null  
/usr/bin/../lib/gcc/x86_64-linux-gnu/9/../../../../include/c++/9/memory_resource:103:20: note: nonnull attribute specified here  
SUMMARY: UndefinedBehaviorSanitizer: undefined-behavior ../include/boost/json/detail/impl/stack.ipp:38:21 in   
  
It looks like https://github.com/CPPAlliance/json/blob/400136a292fda23452c5d914cda7fef70939a2b5/include/boost/json/detail/impl/stack.ipp#L38 uses buf_ without checking it for null.  
  
I also wonder if there is perhaps a copy paste error here, using size_ instead of cap_? Should it perhaps be  
```diff  
--- a/include/boost/json/detail/impl/stack.ipp  
+++ b/include/boost/json/detail/impl/stack.ipp  
@@ -35,7 +35,8 @@ reserve(std::size_t n)  
     if(buf_ && size_ > 0)  
         std::memcpy(  
             buf, buf_, size_);  
-    sp_->deallocate(buf_, size_);  
+    if(buf_)  
+        sp_->deallocate(buf_, cap_);  
     buf_ = buf;  
     cap_ = n;  
 }  
  
```

---

## Comment 1

> Username: pauldreik  
> Created at: 2020-08-18 09:39:52 UTC  
> Url: https://github.com/boostorg/json/issues/166#issuecomment-675375922  

Here is a work in progress branch with a fuzzer and a fix: https://github.com/pauldreik/json/tree/fuzz_02

---

## Comment 2

> Username: vinniefalco  
> Created at: 2020-08-18 11:11:47 UTC  
> Url: https://github.com/boostorg/json/issues/166#issuecomment-675416328  

That fuzzer is no joke, all my mistakes are put on blast lol

---

## Comment 3

> Username: sdkrystian  
> Created at: 2020-08-18 17:29:03 UTC  
> Url: https://github.com/boostorg/json/issues/166#issuecomment-675612668  

@pauldreik Would you like to open a PR with the fix?

---

## Comment 4

> Username: pauldreik  
> Created at: 2020-08-18 17:34:35 UTC  
> Url: https://github.com/boostorg/json/issues/166#issuecomment-675615522  

Sure! Do you think the suggested patch is correct? I have no context at  
all, I just looked at the few surrounding rows and guessed :-)

---

## Comment 5

> Username: sdkrystian  
> Created at: 2020-08-18 17:41:53 UTC  
> Url: https://github.com/boostorg/json/issues/166#issuecomment-675619036  

Yup, you guessed right :)
