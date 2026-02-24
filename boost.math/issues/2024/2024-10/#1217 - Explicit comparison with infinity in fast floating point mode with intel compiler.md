# #1217 - Explicit comparison with infinity in fast floating point mode with intel compiler [Closed]

> Username: pps83  
> Created at: 2024-10-25 00:46:07 UTC  
> Updated at: 2024-10-28 19:47:00 UTC  
> Closed at: 2024-10-28 19:46:59 UTC  
> Url: https://github.com/boostorg/math/issues/1217  

I use vs 2022 with `Intel(R) C++ Compiler Integration 2024`. That's the ICX compiler from intel (free extension that can be installed from visual studio).  
With intel compiler, if I use boost I end up with lots of warnings that look like this:  
  
```  
3>In file included from D:\work-pps\backtest-engine\src\SplitsInfo.cpp:5:  
3>In file included from D:\work-pps\boost_1_86_0\boost\multiprecision\cpp_int.hpp:19:  
3>In file included from D:\work-pps\boost_1_86_0\boost\multiprecision\number.hpp:12:  
3>In file included from D:\work-pps\boost_1_86_0\boost\multiprecision\detail\generic_interconvert.hpp:12:  
3>In file included from D:\work-pps\boost_1_86_0\boost\multiprecision\detail\default_ops.hpp:24:  
3>In file included from D:\work-pps\boost_1_86_0\boost\math\special_functions\next.hpp:17:  
3>In file included from D:\work-pps\boost_1_86_0\boost\math\special_functions\trunc.hpp:23:  
3>In file included from D:\work-pps\boost_1_86_0\boost\math\ccmath\ldexp.hpp:17:  
3>In file included from D:\work-pps\boost_1_86_0\boost\math\ccmath\abs.hpp:19:  
3>D:\work-pps\boost_1_86_0\boost\math\ccmath\isinf.hpp(29,22): warning : explicit comparison with infinity in fast floating point mode [-Wtautological-constant-compare]  
3>   29 |             return x == std::numeric_limits<T>::infinity() || -x == std::numeric_limits<T>::infinity();  
3>      |                    ~ ^  ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
3>D:\work-pps\boost_1_86_0\boost\math\ccmath\ldexp.hpp(50,38): note: in instantiation of function template specialization 'boost::math::ccmath::isinf<double>' requested here  
3>   50 |                (boost::math::ccmath::isinf)(arg) ? arg :  
3>      |                                      ^  
3>D:\work-pps\boost_1_86_0\boost\math\ccmath\ldexp.hpp(64,33): note: in instantiation of function template specialization 'boost::math::ccmath::ldexp<double, true>' requested here  
3>   64 |     return boost::math::ccmath::ldexp(static_cast<double>(arg), exp);  
3>      |                                 ^  
```  
  
Note, I do not use any special compiler specific options/flags. When I compile with clang-cl I don't get any of these warnings, with icx (which is also clang based) I get tons of warnings as shown above. Temporary, to avoid overwhelming warnings I fixed it this way:  
  
```  
--- D:\work-pps\boost_1_86_0\boost\math\ccmath\isinf - Copy.hpp	Fri Oct 25 00:43:06 2024 UTC  
+++ D:\work-pps\boost_1_86_0\boost\math\ccmath\isinf.hpp	Fri Oct 25 00:44:56 2024 UTC  
@@ -22,14 +22,7 @@  
     {  
         if constexpr (std::numeric_limits<T>::is_signed)  
         {  
-#ifdef __clang__  
-#pragma clang diagnostic push  
-#pragma clang diagnostic ignored "-Wtautological-constant-compare"  
-#endif  
             return x == std::numeric_limits<T>::infinity() || -x == std::numeric_limits<T>::infinity();  
-#ifdef __clang__  
-#pragma clang diagnostic pop  
-#endif  
         }  
         else  
         {  
@@ -39,7 +32,7 @@  
     else  
     {  
         using boost::math::isinf;  
-  
+          
         if constexpr (!std::is_integral_v<T>)  
         {  
             return (isinf)(x);  
```

---

## Comment 1

> Username: mborland  
> Created at: 2024-10-25 13:14:17 UTC  
> Url: https://github.com/boostorg/math/issues/1217#issuecomment-2437749589  

Related to: https://github.com/boostorg/math/issues/548. Since these checks are in the consteval path of `isinf` silencing them is really the only way to go.
