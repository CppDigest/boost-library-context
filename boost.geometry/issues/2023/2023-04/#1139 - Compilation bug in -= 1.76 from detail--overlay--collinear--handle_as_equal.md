# #1139 - Compilation bug in >= 1.76 from detail::overlay::collinear::handle_as_equal [Closed]

> Username: kb0n  
> Created at: 2023-04-21 10:50:09 UTC  
> Updated at: 2023-05-08 18:31:02 UTC  
> Closed at: 2023-05-08 18:31:02 UTC  
> Url: https://github.com/boostorg/geometry/issues/1139  

I experience some problems when adapting [Lanelet2 library](https://github.com/fzi-forschungszentrum-informatik/Lanelet2) to boost v1.78.0 , one of which is caused by a bug introduced with release v1.76.0 and has not been fixed yet.  
  
### When was this introduced  
Introduced with 84d3a8d9ceb49489e8c230b92843326f5907417b, specifically this line: https://github.com/boostorg/geometry/commit/84d3a8d9ceb49489e8c230b92843326f5907417b#diff-21d011ddccb292362e430e14bebe3673bfa5e10f432ee3a85ede067fe8f140c6R993 which is still existing in the latest boost version as well.    
  
### Identified issue  
You're using a conditional operator, but `range_q` and `range_p` can be of a different type.  
  
In my case, they were `Eigen::Matrix<double, 2, 1>` and `Eigen::Matrix<double, 2, 1, 2>`, i.e. aligned and non-aligned 2D Eigen-vectors.  
  
### Proposed fix  
The fix is quite simple. This would be a proposal for the current version, but applicable to all other releases as well  
```diff  
-        auto const dm = fun::distance_measure(info.intersections[1],  
-                arrival_p == 1 ? range_q.at(1) : range_p.at(1));  
+        auto const dm = arrival_p == 1  
+               ? fun::distance_measure(info.intersections[1], range_q.at(1))  
+               : fun::distance_measure(info.intersections[1], range_p.at(1));  
```  
It would be nice if this could be backported to all affected revisions as well with a patch-release. I personally would be happy if this was provided "only" from v1.78.0 onward.

---

## Comment 1

> Username: kb0n  
> Created at: 2023-04-21 10:51:42 UTC  
> Url: https://github.com/boostorg/geometry/issues/1139#issuecomment-1517644296  

Of course this is only what I have perceived so far.  
  
I'll re-trigger this error for a more  precise error-message.

---

## Comment 2

> Username: barendgehrels  
> Created at: 2023-04-23 11:46:04 UTC  
> Url: https://github.com/boostorg/geometry/issues/1139#issuecomment-1519045099  

Hi, I cannot reproduce that. Can you verify it still occurs in `1.82`?  
However, I have no problems applying your fix and will do.  
But we won't backport it, because we cannot update previous versions of the whole Boost library because of this bug.

---

## Comment 3

> Username: barendgehrels  
> Created at: 2023-04-23 12:19:51 UTC  
> Url: https://github.com/boostorg/geometry/issues/1139#issuecomment-1519054324  

Now I can reproduce it, if coordinate type is also different. But get more (similar) problems. Will be fixed.
