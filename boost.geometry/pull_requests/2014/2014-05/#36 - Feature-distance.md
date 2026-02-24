# #36 Feature/distance [Merged]

> Username: mkaravel  
> Created at: 2014-05-22 21:38:30 UTC  
> Updated at: 2014-05-23 22:08:43 UTC  
> Merged at: 2014-05-23 22:08:43 UTC  
> Closed at: 2014-05-23 22:08:43 UTC  
> Url: https://github.com/boostorg/geometry/pull/36  

re-factor segment-box distance computation code

---

## Comment 1

> Username: awulkiew  
> Created_at: 2014-05-22 23:58:47 UTC  
> Updated_at: 2014-05-23 17:45:27 UTC  
> Url: https://github.com/boostorg/geometry/pull/36#discussion_r12981044  

In some places get<>() is called without explicitly specified namesapce geometry, in other the namespace is specified. Aside from the consistency, I'm guessing that on some older compilers it could make the user's code ambiguous because get<>() is also defined in the namespace boost.

---

## Comment 2

> Username: awulkiew  
> Created_at: 2014-05-23 00:00:50 UTC  
> Updated_at: 2014-05-23 17:45:27 UTC  
> Url: https://github.com/boostorg/geometry/pull/36#discussion_r12981111  

ReturnType() default ctor is called explicitly, what is the purpose of it? To assign 0? If ReturnType was some user-defined type it could not work as expected. Consider using ReturnType result(0); which would also be more readable.

---

## Comment 3

> Username: awulkiew  
> Created_at: 2014-05-23 00:03:04 UTC  
> Url: https://github.com/boostorg/geometry/pull/36#issuecomment-43958360  

Aside from this pull request. In segment_to_box_2D_generic::apply() there is a run-time parameter check_intersection. Is it set at run-time or could a compile-time template parameter be used instead?

---

## Comment 4

> Username: mkaravel  
> Created_at: 2014-05-23 10:02:56 UTC  
> Url: https://github.com/boostorg/geometry/pull/36#issuecomment-43991270  

@awulkiew : calls to get are now qualified with the geometry namespace  
@awulkiew : result is now initialized with 0. The reason for having the line  
  
```  
ReturnType result = ReturnType();  
```  
  
was to avoid possible uninitialized variable compiler warnings. In any case, following your suggestion makes the code more readable, hence applied.

---

## Comment 5

> Username: mkaravel  
> Created_at: 2014-05-23 10:12:19 UTC  
> Url: https://github.com/boostorg/geometry/pull/36#issuecomment-43991934  

@awulkiew : first of all the segment_to_box_2D_generic code is not the actual code used by distance(segment, box). I added it there:  
1) for debugging purposes (I use it in the unit test to check the result of the actual routine used)  
2) it can be used for non-Cartesian geometries (e.g., for spherical geometries)  
  
Regarding your actual question: it can be made a template parameter, sure. I do not really have a preference about that. The reason for having this argument is to be able to de-activate the intersection check if the segment-box distance routine is called from another distance routine (such as distance(linestring, box) or distance(multilinestring, box)), in which case you would do the intersection check at a higher call level. Even in this case a boolean template would work.  
  
Do you think it would be better to replace the argument by a template parameter?  
If check_intersection is always set to a specific boolean value in the code (I mean hardcoded to true or false), I would expect that the complier optimizes the check in exactly the same way as if it where a template parameter.

---

## Comment 6

> Username: awulkiew  
> Created_at: 2014-05-23 10:58:53 UTC  
> Url: https://github.com/boostorg/geometry/pull/36#issuecomment-43995180  

Yes, a modern compiler should probably optimize this, but you never know. Even with the compile-time parameter you probably can't be sure. To force the compiler to do it, some compile-time dispatching should be made. On the other hand then you couldn't be sure if the function would be inlined ;).  
  
It's not used in the distance() so it doesn't matter. And I doubt that there would be some loss in performance even if it were not optimized. I just thought that in general in BG compile-time constants are passed as template parameters, so this would be consistent.  
  
But I'll leave the decision to you and if nobody have any comments I'll merge this today.

---

## Comment 7

> Username: mkaravel  
> Created_at: 2014-05-23 11:06:53 UTC  
> Url: https://github.com/boostorg/geometry/pull/36#issuecomment-43995699  

There are places in the distance code that I use the same paradigm: pass check_intersection with a default value. The reasons are the same (I want to avoid checking for intersection if this is done at a higher level).  
  
If I am to change this, I would like to do it all over for consistency.  
  
As you said, having or not a performance loss is debatable, so unless there somebody objects I will keep it as it is.

---
