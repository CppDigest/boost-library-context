# #44 - Emscripten compilation issue [Open]

> Username: elcerdo  
> Created at: 2025-01-22 11:09:49 UTC  
> Updated at: 2025-10-18 06:40:29 UTC  
> Url: https://github.com/boostorg/interval/issues/44  

Hello!  
I might do something wrong, but I had to apply the following patch to the boost interval library (v1.82.0) in order to build it successfully using the emscripten toolchain (v3.1.51).  
```  
diff --git a/include/boost/numeric/interval/hw_rounding.hpp b/include/boost/numeric/interval/hw_rounding.hpp  
index 46d452e..4211079 100644  
--- a/include/boost/numeric/interval/hw_rounding.hpp  
+++ b/include/boost/numeric/interval/hw_rounding.hpp  
@@ -16,6 +16,7 @@  
 #include <boost/numeric/interval/rounded_arith.hpp>  
  
 #define BOOST_NUMERIC_INTERVAL_NO_HARDWARE  
+#undef BOOST_NO_FENV_H  
  
 // define appropriate specialization of rounding_control for built-in types  
 #if defined(__x86_64__) && !defined(BOOST_NO_FENV_H)  
```  
This reverts to the c99 impl, which builds and works fine as far I can tell.  
From what I gathered, the definition of `BOOST_NO_FENV_H` occurs in `include/boost/config/platform/wasm.hpp:23` and commenting it out also fixes the issue.  
This is, however, very hacky and I was wondering if there way a cleaner way to achieve the same results.

---

## Comment 1

> Username: mglisse  
> Created at: 2025-10-18 06:40:29 UTC  
> Url: https://github.com/boostorg/interval/issues/44#issuecomment-3417900358  

Wasm does not currently provide a way to set the rounding mode (cf https://github.com/WebAssembly/rounding-mode-control), so you cannot safely use `interval<double>` on that platform. You may still be able to use this library for a type with a custom policy if it does not require rounding.
