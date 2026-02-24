# #1246 fix compilation of converter and is_base_of [Merged]

> Username: barendgehrels  
> Created at: 2024-02-24 20:40:18 UTC  
> Updated at: 2024-04-01 11:29:26 UTC  
> Merged at: 2024-02-26 09:52:10 UTC  
> Closed at: 2024-02-26 09:52:10 UTC  
> Url: https://github.com/boostorg/geometry/pull/1246  

This fixes two new errors:  
* `is_base_of` was called from `Boost`, while in the rest of the library it was called from `std`. Fixed ✅   
* `converter` reported an error, apparently it is changed in the underlying Boost library. Fixed ✅   
  
The first one is trivial. For the second one (related to Boost.Rational) there were three options  
* fix `converter` call - with the risk that it will happen again  
* add `numeric_cast_traits` - I got it working, but it is quite a lot of code, and it will give problems if a library user does it also for Boost.Rational   
* add another level of indirection, as we often do, so add a `geometry::detail::numeric_cast`  
  
I selected the third option. If affected quite some files, but the changes were mechanic.  
  
Add error 1: this was the message  
```  
In file included from /home/runner/work/geometry/geometry/boost-root/libs/geometry/test/headers/main.cpp:14:  
./boost/geometry/algorithms/detail/within/within_no_turns.hpp:145:34: error: ‘is_base_of’ is not a member of ‘boost’; did you mean ‘std::is_base_of’?  
  145 |           bool IsMulti1 = boost::is_base_of<geometry::multi_tag, Tag1>::value,  
      |                                  ^~~~~~~~~~  
  
```  
  
Add error 2: this was the message  
```  
gcc.compile.c++ bin.v2/libs/geometry/test/headers/h-util-rational.test/gcc-11/debug/threading-multi/visibility-hidden/h-util-rational.o  
In file included from /home/runner/work/geometry/geometry/boost-root/libs/geometry/test/headers/main.cpp:14:  
./boost/geometry/util/rational.hpp:150:8: error: ‘converter’ is not a class template  
  150 | struct converter<int, rational<T>, Traits, OverflowHandler, Float2IntRounder, RawConverter, UserRangeChecker>  
      |        ^~~~~~~~~  
./boost/geometry/util/rational.hpp:167:112: error: wrong number of template arguments (7, should be 6)  
  167 | struct converter<double, rational<T>, Traits, OverflowHandler, Float2IntRounder, RawConverter, UserRangeChecker>  
      |                                                                                                                ^  
./boost/geometry/util/rational.hpp:150:109: note: provided for ‘template<class T, class Traits, class OverflowHandler, class Float2IntRounder, class RawConverter, class UserRangeChecker> struct boost::numeric::converter’  
  150 | struct converter<int, rational<T>, Traits, OverflowHandler, Float2IntRounder, RawConverter, UserRangeChecker>  
      |                                                                                                             ^  
```

---

## Comment 1

> Username: awulkiew  
> Created_at: 2024-02-24 21:04:41 UTC  
> Url: https://github.com/boostorg/geometry/pull/1246#issuecomment-1962731131  

I noticed that numeric converter is failing and wanted to propose adding missing header  
```  
#include <boost/numeric/conversion/converter.hpp>  
```  
because it seems to fix the compilation. Is this one of the solutions or is it the fourth one?  
  
Anyway, I think the solution you've choosen is better.  
  
Btw, in util/rational.hpp there is also a specialization of `boost::numeric::bounds<>` for `rational<T>`. Is it still needed?

---

## Review 2 [Commented]

> Username: awulkiew  
> Created_at: 2024-02-24 21:33:27 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1246#pullrequestreview-1899570453  

📁 include/boost/geometry/util/numeric_cast.hpp

```diff
  41 |+ 
  42 |+ template <typename Target, typename Source>
  43 |+ inline Target numeric_cast(Source const& source)
```

> Username: awulkiew  
> Created_at: 2024-02-24 21:33:27 UTC  
> Url: https://github.com/boostorg/geometry/pull/1246#discussion_r1501701022  

Why not put it in `bg::util` namespace with the rest of utilities?

> Username: barendgehrels  
> Created_at: 2024-02-24 21:38:44 UTC  
> Url: https://github.com/boostorg/geometry/pull/1246#discussion_r1501701602  

I noticed that `coordinate_cast` was also in `detail` so I put it there.  
Also it is not really meant for end users (but `util` neither).  
I didn't think of `util` - yes, maybe it is better there. I'll change it tomorrow.

> Username: barendgehrels  
> Created_at: 2024-02-25 11:09:28 UTC  
> Updated_at: 2024-02-25 11:09:29 UTC  
> Url: https://github.com/boostorg/geometry/pull/1246#discussion_r1501797054  

✅ changed it to `util::`


---

## Review 3 [Commented]

> Username: awulkiew  
> Created_at: 2024-02-24 21:36:41 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1246#pullrequestreview-1899570748  

📁 include/boost/geometry/algorithms/convert.hpp

```diff
  82 |         set<Index, Dimension>(box,
  83 |-                 boost::numeric_cast<coordinate_type>(get<Dimension>(point)));
  83 |+                 geometry::detail::numeric_cast<coordinate_type>(get<Dimension>(point)));
```

> Username: awulkiew  
> Created_at: 2024-02-24 21:36:41 UTC  
> Url: https://github.com/boostorg/geometry/pull/1246#discussion_r1501701309  

This could be `detail::numeric_cast` or if this was implemented in `util` namspace `util::numeric_cast`.

> Username: barendgehrels  
> Created_at: 2024-02-24 21:37:49 UTC  
> Url: https://github.com/boostorg/geometry/pull/1246#discussion_r1501701483  

`detail` didn't always work, because we use it a lot on lower levels too.

> Username: barendgehrels  
> Created_at: 2024-02-25 11:09:59 UTC  
> Url: https://github.com/boostorg/geometry/pull/1246#discussion_r1501797162  

for `util` the `geometry::` prefix was not necessary, it's convenient


---

## Comment 4

> Username: awulkiew  
> Created_at: 2024-02-24 21:39:02 UTC  
> Url: https://github.com/boostorg/geometry/pull/1246#issuecomment-1962737421  

It seems PR https://github.com/boostorg/geometry/pull/1243 created by @vissarion also fixes the conversion issue.

---

## Comment 5

> Username: barendgehrels  
> Created_at: 2024-02-24 21:41:30 UTC  
> Url: https://github.com/boostorg/geometry/pull/1246#issuecomment-1962737928  

> I noticed that numeric converter is failing and wanted to propose adding missing header  
  
To be honest, I didn't try this. The error message indicated something else. But of course, if you tried it, and it worked, it might have solved it.  
  
>   
> Anyway, I think the solution you've choosen is better.  
  
OK let's keep it then.  
   
> Btw, in util/rational.hpp there is also a specialization of `boost::numeric::bounds<>` for `rational<T>`. Is it still needed?  
  
Yes, I saw it too. I didn't want to mix in other things. But it might be that it is not needed anymore.

---

## Comment 6

> Username: barendgehrels  
> Created_at: 2024-02-25 11:22:20 UTC  
> Url: https://github.com/boostorg/geometry/pull/1246#issuecomment-1962900337  

> > Btw, in util/rational.hpp there is also a specialization of `boost::numeric::bounds<>` for `rational<T>`. Is it still needed?  
>   
> Yes, I saw it too. I didn't want to mix in other things. But it might be that it is not needed anymore.  
  
I think it is still needed, because of this code in `assign_values.hpp`:  
```  
        coordinate_type const highest = geometry::bounds<coordinate_type>::highest();  
        coordinate_type const lowest = geometry::bounds<coordinate_type>::lowest();  
```  
Now this is actually not yet optimal, because that is defined like this, in `is_inverse_spheroidal_coordinates.hpp`:  
```  
namespace boost { namespace geometry  
{  
  
template<class CT>  
struct bounds  
{  
  static CT lowest  () { return boost::numeric::bounds<CT>::lowest(); }  
  static CT highest () { return boost::numeric::bounds<CT>::highest(); }  
};  
```  
  
So it looks like a generic structure. But most of the code uses it from `boost::numeric::` directly.  
So I think that should go to a specific  headerfile in `util` as well. And then we might make it for Boost.Rational specifically, like I did for `numeric_cast`.  
  
Anyway, for Boost.Rational it is still needed now, and it is better to handle it in a separate PR.

---

## Review 7 [Commented]

> Username: awulkiew  
> Created_at: 2024-02-25 11:48:21 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1246#pullrequestreview-1899660342  

📁 include/boost/geometry/algorithms/detail/within/within_no_turns.hpp

```diff
 146 |-           bool IsMulti2 = boost::is_base_of<geometry::multi_tag, Tag2>::value>
 148 |+           bool IsMulti1 = std::is_base_of<geometry::multi_tag, Tag1>::value,
 149 |+           bool IsMulti2 = std::is_base_of<geometry::multi_tag, Tag2>::value>
```

> Username: awulkiew  
> Created_at: 2024-02-25 11:48:21 UTC  
> Url: https://github.com/boostorg/geometry/pull/1246#discussion_r1501803504  

This could also be replaced with `util::is_multi<Geometry2>::value` from `<boost/geometry/util/type_traits.hpp>`

> Username: barendgehrels  
> Created_at: 2024-02-25 16:01:48 UTC  
> Url: https://github.com/boostorg/geometry/pull/1246#discussion_r1501849292  

That's right. So this was not the right fix either ;-)  
✅ fixed, and on 3 other places as well.


---

## Comment 8

> Username: awulkiew  
> Created_at: 2024-02-25 11:52:29 UTC  
> Url: https://github.com/boostorg/geometry/pull/1246#issuecomment-1962908821  

> it is better to handle it in a separate PR  
  
I agree. Thanks for the investigation and explanation.

---

## Review 9 [Commented]

> Username: awulkiew  
> Created_at: 2024-02-25 11:56:26 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1246#pullrequestreview-1899661687  

📁 include/boost/geometry/util/numeric_cast.hpp

```diff
  51 |+ inline Target numeric_cast(Source const& source)
  52 |+ {
  53 |+     return detail::numeric_caster<Target, Source>::apply(source);
```

> Username: awulkiew  
> Created_at: 2024-02-25 11:56:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/1246#discussion_r1501804982  

It seems that there is namespace `util::detail` so:  
```diff  
-    return detail::numeric_caster<Target, Source>::apply(source);  
+    return geometry::detail::numeric_caster<Target, Source>::apply(source);  
```  
Or alternatively `numeric_caster` could be moved from `geometry::detail`.

> Username: barendgehrels  
> Created_at: 2024-02-25 12:56:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/1246#discussion_r1501815859  

👍 will update.

> Username: barendgehrels  
> Created_at: 2024-02-25 16:02:13 UTC  
> Url: https://github.com/boostorg/geometry/pull/1246#discussion_r1501849408  

✅ fixed (will update PR)


---

## Comment 10

> Username: barendgehrels  
> Created_at: 2024-02-25 16:05:41 UTC  
> Updated_at: 2024-02-25 16:06:10 UTC  
> Url: https://github.com/boostorg/geometry/pull/1246#issuecomment-1962985011  

The file `within_no_turns.hpp` is now updated.  
But the whole file is never used anywhere, as far as I can see. And all is in `detail` so it is not public.  
So we can deprecate it (I remember this was already discussed earlier a while ago).  
  
Also there are no unit tests.  
  
Anyway, it's now fixed, but can be removed later.  
  
Question then: how did CI detect it didn't work?

---

## Review 11 [Approved]

> Username: vissarion  
> Created_at: 2024-02-26 09:49:15 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/1246#pullrequestreview-1900403116  

Thanks. I agree with the changes. I will close https://github.com/boostorg/geometry/pull/1243 as duplicate.

---

## Comment 12

> Username: vissarion  
> Created_at: 2024-02-26 09:52:05 UTC  
> Url: https://github.com/boostorg/geometry/pull/1246#issuecomment-1963716084  

> Question then: how did CI detect it didn't work?  
  
We have a "headers" script in CI that tests all the header files of the library if they compile independently (related issue https://github.com/boostorg/geometry/issues/523)  
  
See  
https://github.com/boostorg/geometry/actions/runs/8046744097/job/21974572517   
https://github.com/boostorg/geometry/blob/develop/.github/workflows/headers.yml

---

## Comment 13

> Username: barendgehrels  
> Created_at: 2024-02-26 20:35:06 UTC  
> Url: https://github.com/boostorg/geometry/pull/1246#issuecomment-1965220496  

> > Question then: how did CI detect it didn't work?  
>   
> We have a "headers" script in CI that tests all the header files of the library if they compile independently (related issue #523)  
>   
> See https://github.com/boostorg/geometry/actions/runs/8046744097/job/21974572517 https://github.com/boostorg/geometry/blob/develop/.github/workflows/headers.yml  
  
👍 thanks, good to know! And thanks for approval and merging!

---
