# #673 refactor: Move GSoC 2020 features to extension [Merged]

> Username: simmplecoder  
> Created at: 2022-05-17 20:26:39 UTC  
> Updated at: 2022-06-03 06:48:11 UTC  
> Merged at: 2022-06-03 06:48:11 UTC  
> Closed at: 2022-06-03 06:48:11 UTC  
> Url: https://github.com/boostorg/gil/pull/673  

### Description  
  
Moves functionality implemented in GSoC 2020 by Olzhas into extension and improves documentation with some fiixups needed.  
  
### References  
  
[Discussion link](https://github.com/boostorg/gil/discussions/668#discussioncomment-2748555).  
  
### Tasklist  
  
- [X] Move rasterization to extension  
- [X] Move Hough transform to extension  
- [X] Move anisotropic diffusion into extension  
- <s>[ ] Add release notes</s> Were already added  
- [x] Ensure all CI builds pass  
- [x] Review and approve

---

## Comment 1

> Username: simmplecoder  
> Created_at: 2022-05-17 20:28:24 UTC  
> Url: https://github.com/boostorg/gil/pull/673#issuecomment-1129287643  

This commit is not complete, but is ready to be merged if needed. I will keep adding on it by strict improvements (so that each commit would still leave it in mergeable state).

---

## Review 2 [Changes requested]

> Username: mloskot  
> Created_at: 2022-05-17 20:28:57 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/gil/pull/673#pullrequestreview-976055396  

📁 include/boost/gil/extension/rasterization/circle.hpp

```diff
  13 |- #include <boost/gil/point.hpp>
  12 |+ #include "boost/gil/detail/math.hpp"
  13 |+ #include "boost/gil/point.hpp"
```

> Username: mloskot  
> Created_at: 2022-05-17 20:28:41 UTC  
> Updated_at: 2022-05-17 20:28:57 UTC  
> Url: https://github.com/boostorg/gil/pull/673#discussion_r875235211  

Why change the convention?

> Username: simmplecoder  
> Created_at: 2022-05-17 20:32:39 UTC  
> Updated_at: 2022-05-17 20:32:40 UTC  
> Url: https://github.com/boostorg/gil/pull/673#discussion_r875238461  

Thanks for catching it. My guess is that IDE did that, but I don't know for sure.

> Username: mloskot  
> Created_at: 2022-05-17 20:33:38 UTC  
> Url: https://github.com/boostorg/gil/pull/673#discussion_r875239163  

I see. Let's stick to the Boost-wide convention of `<>`

> Username: simmplecoder  
> Created_at: 2022-05-17 20:49:37 UTC  
> Url: https://github.com/boostorg/gil/pull/673#discussion_r875250534  

I found the reason. It is CLion's "refactoring" that happens when files are moved. Just FYI if you use CLion.

> Username: mloskot  
> Created_at: 2022-05-17 21:07:11 UTC  
> Updated_at: 2022-05-17 21:07:12 UTC  
> Url: https://github.com/boostorg/gil/pull/673#discussion_r875262509  

I see. I use CLion from time to time. Good to know


---

## Review 3 [Changes requested]

> Username: mloskot  
> Created_at: 2022-05-27 06:57:00 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/gil/pull/673#pullrequestreview-987114900  

The `Jamfile`s need to be corrected.

📁 test/extension/rasterization/Jamfile

```diff
  15 | run line.cpp ;
  16 | run circle.cpp ;
  17 | run ellipse.cpp ;
```

> Username: mloskot  
> Created_at: 2022-05-27 06:54:44 UTC  
> Updated_at: 2022-05-27 06:57:00 UTC  
> Url: https://github.com/boostorg/gil/pull/673#discussion_r883330285  

Repeating `compile` and `run` for the same tests is not necessary.  
- `compile` is for compile-only tests e.g. static asserts, nothing executed  
- `run` is for tests you aim to build and execute


📁 test/extension/image_processing/Jamfile

```diff
  17 |+ run hough_circle_transform.cpp ;
  18 |+ run hough_line_transform.cpp ;
  19 |+ run hough_parameter.cpp ;
```

> Username: mloskot  
> Created_at: 2022-05-27 06:56:28 UTC  
> Updated_at: 2022-05-27 06:57:00 UTC  
> Url: https://github.com/boostorg/gil/pull/673#discussion_r883331609  

Same here, repeating `compile` and `run` for the same tests is not necessary.   
- `compile` is for compile-only tests e.g. static asserts, nothing executed  
- `run` is for tests you aim to build and execute


---

## Comment 4

> Username: marco-langer  
> Created_at: 2022-06-02 04:02:10 UTC  
> Updated_at: 2022-06-02 05:55:58 UTC  
> Url: https://github.com/boostorg/gil/pull/673#issuecomment-1144410472  

Some refactor suggestions:  
  
* line.hpp is missing include guards.  
* d_first in line.hpp and circle.hpp models OutputIterator category and not RandomAccessIterator category  
* mixing of east and west const in line.hpp  
* trailing return types  
* `namespace boost { namespace gil {` should be a one-liner in line.hpp

---

## Comment 5

> Username: mloskot  
> Created_at: 2022-06-02 07:32:55 UTC  
> Url: https://github.com/boostorg/gil/pull/673#issuecomment-1144542731  

Thank you @marco-langer   
AFAIK, @simmplecoder is preparing to move home at the moment.  
  
I think we should merge this, unless you think this PR should not be merged, then we can apply further refactoring in separate PRs.

---
