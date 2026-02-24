# #428 - Boost interpolators code should be C++-14 compatible [Closed]

> Username: martlaak-gmail  
> Created at: 2020-09-01 11:24:13 UTC  
> Updated at: 2020-09-03 06:05:11 UTC  
> Closed at: 2020-09-03 06:05:11 UTC  
> Url: https://github.com/boostorg/math/issues/428  

Please make Boost interpolators C++-14 compatible. Can be done easily, with this kind of little patch (there are other files probably as well to be patched like that):  
```  
diff --git a/include/boost/math/interpolators/detail/cubic_hermite_detail.hpp b/include/boost/math/interpolators/detail/cubic_hermite_detail.hpp  
index 6365e1ab6..825851d30 100644  
--- a/include/boost/math/interpolators/detail/cubic_hermite_detail.hpp  
+++ b/include/boost/math/interpolators/detail/cubic_hermite_detail.hpp  
@@ -13,7 +13,10 @@  
 #include <sstream>  
 #include <limits>  
   
-namespace boost::math::interpolators::detail {  
+namespace boost {  
+namespace math {  
+namespace interpolators {  
+namespace detail {  
   
 template<class RandomAccessContainer>  
 class cubic_hermite_detail {  
@@ -426,5 +429,8 @@ private:  
 };  
   
   
+}  
+}  
+}  
 }  
 #endif  
diff --git a/include/boost/math/interpolators/makima.hpp b/include/boost/math/interpolators/makima.hpp  
index 4284073ca..e1dc57f71 100644  
--- a/include/boost/math/interpolators/makima.hpp  
+++ b/include/boost/math/interpolators/makima.hpp  
@@ -13,7 +13,9 @@  
 #include <cmath>  
 #include <boost/math/interpolators/detail/cubic_hermite_detail.hpp>  
   
-namespace boost::math::interpolators {  
+namespace boost {  
+namespace math {  
+namespace interpolators {  
   
 template<class RandomAccessContainer>  
 class makima {  
@@ -170,5 +172,7 @@ private:  
     std::shared_ptr<detail::cubic_hermite_detail<RandomAccessContainer>> impl_;  
 };  
   
+}  
+}  
 }  
 #endif  
\ No newline at end of file  
diff --git a/include/boost/math/interpolators/pchip.hpp b/include/boost/math/interpolators/pchip.hpp  
index 6b6db5cce..e8919a9e4 100644  
--- a/include/boost/math/interpolators/pchip.hpp  
+++ b/include/boost/math/interpolators/pchip.hpp  
@@ -9,7 +9,9 @@  
 #include <memory>  
 #include <boost/math/interpolators/detail/cubic_hermite_detail.hpp>  
   
-namespace boost::math::interpolators {  
+namespace boost {  
+namespace math {  
+namespace interpolators {  
   
 template<class RandomAccessContainer>  
 class pchip {  
@@ -116,5 +118,7 @@ private:  
     std::shared_ptr<detail::cubic_hermite_detail<RandomAccessContainer>> impl_;  
 };  
   
+}  
+}  
 }  
 #endif  
```

---

## Comment 1

> Username: NAThompson  
> Created at: 2020-09-01 12:54:24 UTC  
> Url: https://github.com/boostorg/math/issues/428#issuecomment-684830820  

I did this because I wanted to be able to make any change I felt necessary using up to C++-17 while retaining backward compatibility; furthermore I plan to do this with all subsequent features I add.  
  
The tension here is that if we start adding in all the shims to make things work in C++-14, then we have to couple the code to `boost/config.hpp`, and then every file is dependent on every other file, and users complain they have to deploy the entire boost download to their users.  
  
I personally haven't struggled to find a C++-17 compliant compiler, nor have I experienced pain in the C++-14 -> C++-17 transition. This isn't to say that I'm outright rejecting this diff, it's that I'd rather you be quite sure that changing this isn't harder than simply changing a compiler flag and maybe your compiler.

---

## Comment 2

> Username: martlaak-gmail  
> Created at: 2020-09-01 13:41:34 UTC  
> Url: https://github.com/boostorg/math/issues/428#issuecomment-684862211  

I haven't created the issue when it was so easy. I have to build my project on system having GCC 5.5. Sorry to hear that you can not keep Boost math C++-14 compatible :(

---

## Comment 3

> Username: NAThompson  
> Created at: 2020-09-01 13:44:40 UTC  
> Url: https://github.com/boostorg/math/issues/428#issuecomment-684864399  

You can't `mload` a newer compiler or talk to your sysadmin about making one available?  
  
If not, go ahead and submit a PR with these diffs.

---

## Comment 4

> Username: pabristow  
> Created at: 2020-09-01 13:44:41 UTC  
> Url: https://github.com/boostorg/math/issues/428#issuecomment-684864410  

Sympathy.    
But it would help us at Boost to know "Why?"  GCC is almost up to 11.0.0  and is free.  Who or what is stopping you getting a little more up-to-date?

---

## Comment 5

> Username: NAThompson  
> Created at: 2020-09-01 14:04:21 UTC  
> Url: https://github.com/boostorg/math/issues/428#issuecomment-684878754  

> Who or what is stopping you getting a little more up-to-date?  
  
Yeah I second this. I'm afraid you're stuck in a pathological situation, and that this pathology will spread into Boost. The most correct solution is for you to talk to your sysadmin.

---

## Comment 6

> Username: pabristow  
> Created at: 2020-09-01 16:51:23 UTC  
> Url: https://github.com/boostorg/math/issues/428#issuecomment-684995221  

At Boost.Math we try not to make existing systems obsolete, so that old programs using C++03 will continue to work as before (though we are moving the minimum requirement up to C++11 and will probably move to the most recent standard version in time).    
  
If you are adding new code, you really should be using a new-ish compiler (that does not contain many bugs, now fixed).

---

## Comment 7

> Username: martlaak-gmail  
> Created at: 2020-09-01 18:58:59 UTC  
> Url: https://github.com/boostorg/math/issues/428#issuecomment-685070020  

> If not, go ahead and submit a PR with these diffs.  
  
I'd like to, but have now permission to create branches in Booth.Math repo :(

---

## Comment 8

> Username: NAThompson  
> Created at: 2020-09-01 19:01:02 UTC  
> Url: https://github.com/boostorg/math/issues/428#issuecomment-685071082  

@martlaak-gmail : You don't need permission to do that, just fork the repo and make a branch in your fork.
