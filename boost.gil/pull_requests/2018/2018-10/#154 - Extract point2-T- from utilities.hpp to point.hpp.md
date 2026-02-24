# #154 Extract point2<T> from utilities.hpp to point.hpp [Merged]

> Username: mloskot  
> Created at: 2018-10-16 20:22:40 UTC  
> Updated at: 2018-10-18 21:27:42 UTC  
> Merged at: 2018-10-18 19:36:33 UTC  
> Closed at: 2018-10-18 19:36:33 UTC  
> Url: https://github.com/boostorg/gil/pull/154  

The point belongs to core basic concepts in GIL, not an optional utility. The tutorial starts with description of point.  
  
Such core concepts are defined in dedicated headers which is quite a useful convention that also makes the code structure clearer.  
  
Clean up point.hpp formatting (eg. respect line length limit).  
### Tasklist  
  
- [x] ~Rename `point2` to `point` and add `using point2 = point;` for backward compatibility (see @stefanseefeld suggestion below)~ - moved to separate PR, see https://github.com/boostorg/gil/pull/154#issuecomment-431106310 and finally #155   
- [x] Review  
- [x] Adjust for comments  
- [x] All CI builds and checks have passed  
  
------  
  
@stefanseefeld suggested on Gitter:  
> why don't we use point<> rather than point2<> ?  
> Given that this is not a generic "Point" class, but one specific to (2D) image processing, the '2' is implicit.  
> I'd thus suggest we rename it to point, and then add a using point2 = point; for backward compatibility.

---

## Review 1 [Commented]

> Username: Kojoley  
> Created_at: 2018-10-18 15:40:55 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/gil/pull/154#pullrequestreview-166160911  

You can also make the point type constexpr.

📁 include/boost/gil/point.hpp

```diff
  48 |+     point2(T px, T py) : x(px), y(py) {}
  49 |+     point2(point2 const& p) : x(p.x), y(p.y) {}
  50 |+     ~point2() {}
```

> Username: Kojoley  
> Created_at: 2018-10-18 15:37:03 UTC  
> Updated_at: 2018-10-18 18:07:20 UTC  
> Url: https://github.com/boostorg/gil/pull/154#discussion_r226357037  

Same as above.  
```suggestion  
```


---

## Review 2 [Commented]

> Username: Kojoley  
> Created_at: 2018-10-18 15:42:46 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/gil/pull/154#pullrequestreview-166163917  

📁 include/boost/gil/point.hpp

```diff
  47 |+     point2() = default;
  48 |+     point2(T px, T py) : x(px), y(py) {}
  49 |+     point2(point2 const& p) : x(p.x), y(p.y) {}
```

> Username: Kojoley  
> Created_at: 2018-10-18 15:42:46 UTC  
> Updated_at: 2018-10-18 18:07:20 UTC  
> Url: https://github.com/boostorg/gil/pull/154#discussion_r226359408  

This is not required and makes the type non-trivial even when its members are.  
```suggestion  
```


---

## Review 3 [Commented]

> Username: Kojoley  
> Created_at: 2018-10-18 15:43:00 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/gil/pull/154#pullrequestreview-166164028  

📁 include/boost/gil/point.hpp

```diff
  50 |+     ~point2() {}
  51 |+ 
  52 |+     point2& operator=(point2 const& p)
```

> Username: Kojoley  
> Created_at: 2018-10-18 15:43:00 UTC  
> Updated_at: 2018-10-18 18:07:20 UTC  
> Url: https://github.com/boostorg/gil/pull/154#discussion_r226359504  

Same as above.  
```suggestion  
```


---

## Comment 4

> Username: mloskot  
> Created_at: 2018-10-18 17:08:16 UTC  
> Url: https://github.com/boostorg/gil/pull/154#issuecomment-431088337  

Some good suggestions here, thanks!  
  
Made me think about merging the PR as is, then submitting new PR with `point2` refactored as suggested. Unless, I won't be able to resist trying out the new [Suggested Change](https://blog.github.com/changelog/2018-10-16-suggested-changes/) feature.  
Especially that:  
> The person who suggested the change will be the author of the commit, and you will be the committer of the commit.  
~ https://help.github.com/articles/incorporating-feedback-in-your-pull-request/  
  
This is really nice to give credit even for micro-contributions!

---

## Comment 5

> Username: Kojoley  
> Created_at: 2018-10-18 17:15:38 UTC  
> Url: https://github.com/boostorg/gil/pull/154#issuecomment-431090678  

This is a nice new feature, but I did not find how to suggest multiline edits (so you just removed copy assignment declaration, but the body is still there :smiley:).

---

## Comment 6

> Username: mloskot  
> Created_at: 2018-10-18 17:23:40 UTC  
> Url: https://github.com/boostorg/gil/pull/154#issuecomment-431093149  

@Kojoley Ha! Good catch. No worries, I'll fix it.  
Good we're experimenting in a pull request, but not the develop branch :)

---

## Comment 7

> Username: mloskot  
> Created_at: 2018-10-18 18:03:33 UTC  
> Url: https://github.com/boostorg/gil/pull/154#issuecomment-431106310  

@stefanseefeld There is slight issue with your suggestion of `using point2`. Rather than a problem this should be a reason to clean up GIL further, so let's see :)  
  
In `point.hpp` is this alias template put below `point` class template definition:  
  
```  
template <typename T>  
using point2 = point<T>;  
```  
  
Now, we've got numerous forward declarations of `template <typename T> class point2;`, for example, in `concepts.hpp` which are conflicting with the `point2` alias template. AFAIK, there is no way to forward declare an alias template.  
  
Solution is replace all `point2` with `point` across the whole GIL source code.  
Since volume of this change will be much bigger, I'll submit it in separate PR.

---

## Comment 8

> Username: mloskot  
> Created_at: 2018-10-18 18:15:07 UTC  
> Url: https://github.com/boostorg/gil/pull/154#issuecomment-431109919  

@Kojoley Once the CI builds complete as green, I have two choices:  
  
1. Hit "Squash and merge" then I don't think your authorship will be preserved as commits co-author.  
2. Hit "Merge", then all commits will be preserved  
  
Squashing will help to keep the history free from possibly confusing commits sequence - the two-step removal of the copy assignment :)  
  
If you see how the 1. could be improved, let me know.  
  
/me still learning GitHub

---

## Comment 9

> Username: Kojoley  
> Created_at: 2018-10-18 18:39:01 UTC  
> Url: https://github.com/boostorg/gil/pull/154#issuecomment-431117559  

@mloskot AFAIK it should not, but the whole squashed commit will have two authors. I have nothing against loosing my authorship on these micro-changes so let's try and see how the things actually works :-)

---

## Comment 10

> Username: mloskot  
> Created_at: 2018-10-18 18:53:14 UTC  
> Url: https://github.com/boostorg/gil/pull/154#issuecomment-431121893  

@Kojoley OK, I will Merge and Squash, and let's see how it works. Thanks!

---

## Comment 11

> Username: mloskot  
> Created_at: 2018-10-18 19:42:36 UTC  
> Url: https://github.com/boostorg/gil/pull/154#issuecomment-431136767  

@Kojoley FYI, "Merge and Squash" does not preserve any authorship information for the commits based on the suggested changes. I manually amended b4c3a694796d7cdbf7d0d09001b8c7cdf6c2b804 with the mention.

---
