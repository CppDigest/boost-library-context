# #512 Add implementation of Hough transforms [Merged]

> Username: simmplecoder  
> Created at: 2020-08-04 21:04:52 UTC  
> Updated at: 2021-01-25 22:31:40 UTC  
> Merged at: 2021-01-25 22:31:39 UTC  
> Closed at: 2021-01-25 22:31:40 UTC  
> Url: https://github.com/boostorg/gil/pull/512  

### Description  
  
Adds Hough line, circle and ellipse transforms. Also adds support for line and circle rasterization  
  
### References  
  
http://groups.csail.mit.edu/graphics/classes/6.837/F02/lectures/6.837-7_Line.pdf  
  
https://en.wikipedia.org/wiki/Hough_transform#:~:text=The%20Hough%20transform%20is%20a,shapes%20by%20a%20voting%20procedure.  
  
### Tasklist  
  
<!-- Add YOUR OWN TASK(s), especially if your PR is a work in progress -->  
- [X] Implement Hough line transform  
- [X] Test Hough line transform  
- [X] Implement line rasterization   
- [X] Test line rasterization  
- [X] Implement Hough circle transform  
- [X] Test Hough circle transform  
<S>- [ ] Implement Hough ellipse transform  
- [ ] Test Hough ellipse transform</S>  
- [ ] Ensure all CI builds pass - RIP Travis CI. Long live our [GitHub Actions](https://github.com/boostorg/gil/actions) setup!  
- [x] Review and approve

---

## Review 1 [Changes requested]

> Username: mloskot  
> Created_at: 2020-08-25 17:35:51 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/gil/pull/512#pullrequestreview-474676319  

@simmplecoder Here is my first round of comments to this PR. See also [my question on Slack](https://cpplang.slack.com/archives/CSVT0STV2/p1598372127031300)

📁 example/circle.cpp

```diff
   6 |+ #include <boost/gil/image_view.hpp>
   7 |+ #include <boost/gil/rasterization/circle.hpp>
   8 |+ #include <boost/gil/typedefs.hpp>
```

> Username: mloskot  
> Created_at: 2020-08-25 16:59:00 UTC  
> Updated_at: 2021-01-25 17:57:40 UTC  
> Url: https://github.com/boostorg/gil/pull/512#discussion_r476598650  

This as well as in all other `.cpp` files  
  
- The copyright is missing.  
- This should be simplified to   
  
```cpp  
#include <boost/gil.hpp>  
#include <boost/gil/extension/io/png.hpp>  
#include <boost/gil/rasterization/line.hpp>  
```  
  
or even  
  
```cpp  
#include <boost/gil.hpp>  
#include <boost/gil/extension/io/png.hpp>  
```  
  
assuming `#include <boost/gil/rasterization/*>` are added to `<boost/gil.hpp>`

---

📁 example/circle.cpp

```diff
   1 |+ #include "boost/gil/extension/io/png/tags.hpp"
```

> Username: mloskot  
> Created_at: 2020-08-25 16:59:33 UTC  
> Updated_at: 2021-01-25 17:57:40 UTC  
> Url: https://github.com/boostorg/gil/pull/512#discussion_r476598999  

I'd rename `circle.cpp` to `rasterizer_circle.cpp`


📁 example/line.cpp

```diff
   1 |+ #include "boost/gil/extension/io/png/tags.hpp"
```

> Username: mloskot  
> Created_at: 2020-08-25 16:59:48 UTC  
> Updated_at: 2021-01-25 17:57:40 UTC  
> Url: https://github.com/boostorg/gil/pull/512#discussion_r476599149  

I'd rename `line.cpp` to `rasterizer_line.cpp`


📁 example/hough_circle_transform.cpp

```diff
   1 |+ #include <boost/gil/extension/io/png.hpp>
```

> Username: mloskot  
> Created_at: 2020-08-25 17:00:12 UTC  
> Updated_at: 2021-01-25 17:57:40 UTC  
> Url: https://github.com/boostorg/gil/pull/512#discussion_r476599435  

I'd rename `hough_circle_transform.cpp` to `hough_transform_circle.cpp`


📁 example/hough_line_transform.cpp

```diff
   1 |+ #include <boost/gil/detail/math.hpp>
```

> Username: mloskot  
> Created_at: 2020-08-25 17:00:35 UTC  
> Updated_at: 2021-01-25 17:57:40 UTC  
> Url: https://github.com/boostorg/gil/pull/512#discussion_r476599685  

I'd rename `hough_line_transform.cpp` to `hough_transform_line.cpp`


📁 include/boost/gil/rasterization/line.hpp

```diff
  33 |+ 
  34 |+     template <typename RAIterator>
  35 |+     void operator()(std::ptrdiff_t width, std::ptrdiff_t height, RAIterator output_first) const
```

> Username: mloskot  
> Created_at: 2020-08-25 17:02:56 UTC  
> Updated_at: 2021-01-25 17:57:40 UTC  
> Url: https://github.com/boostorg/gil/pull/512#discussion_r476601025  

1. Why the rasterizer does not take coordinates of from point and to point?  
    I'm not clear why it has the fixed `point_t(0, 0);` later below.  
  
2. Why this is a function object? Why not `line_rasterizer` taking `bresenham_rasterizer` strategy type and applied from start point to end point? Public function overloads  
  
```cpp  
rasterize_line(from, to, output);  
output = rasterize_line(from, to);  
```

> Username: simmplecoder  
> Created_at: 2020-08-25 21:23:00 UTC  
> Updated_at: 2021-01-25 17:57:40 UTC  
> Url: https://github.com/boostorg/gil/pull/512#discussion_r476747245  

1. That interface is probably better, yes. I don't really remember why I chose to go with `width` and `height`. I will rework this tomorrow.  
  
2. I wanted it to be easy to pass into function templates. Circle rasterizers for example are passed into Hough transform for circles.

> Username: mloskot  
> Created_at: 2020-08-26 09:40:06 UTC  
> Updated_at: 2021-01-25 17:57:40 UTC  
> Url: https://github.com/boostorg/gil/pull/512#discussion_r477172432  

> 2. I wanted it to be easy to pass into function templates.  
>     Circle rasterizers for example are passed into Hough transform for circles.  
  
If rasteriser is dedicated as public functionality for users, I'd consider its direct call as primary use case and design its interfaces with end-user convenience in mind.  
Then, passing the rasterisers into other functions would be a secondary use-case - it can be wrapped with function-local `struct` or lambda.  
  
However, we are not required to release the rasterisers as part of the public interfaces now - our focus is on the Hough transforms here. You can just move it into `boost::gil::detail` and keep it as it is. W can release the rasterisers after we polish their interface, in future.  
  
Perhaps @lpranam has an advice/opinion on that.

> Username: simmplecoder  
> Created_at: 2020-09-04 20:59:13 UTC  
> Updated_at: 2021-01-25 17:57:40 UTC  
> Url: https://github.com/boostorg/gil/pull/512#discussion_r483841570  

It was much harder than I thought, but I implemented two point version of the interface. Hough transform for lines tests just worked after small modifications.

> Username: simmplecoder  
> Created_at: 2020-09-04 21:02:43 UTC  
> Updated_at: 2021-01-25 17:57:40 UTC  
> Url: https://github.com/boostorg/gil/pull/512#discussion_r483842723  

The testing has breadth and depth, but there is a design decision problem: current implementation does not guarantee equivalence of a line drawn in different direction to the current one. I could use canonical direction, but am not sure if it is worth it. See the tests for more context. I believe now it is usable by broader public, but I guess some more views wouldn't hurt.

> Username: simmplecoder  
> Created_at: 2020-09-04 21:09:50 UTC  
> Updated_at: 2021-01-25 17:57:40 UTC  
> Url: https://github.com/boostorg/gil/pull/512#discussion_r483845025  

As for the filling the pixels themselves not being one step process with rasterization, we could add something like this:  
  
    template <typename View, typename Rasterizer, typename ... RasterizerArgs>  
    void render(View const& view, typename View::value_type fill_pixel, Rasterizer& rasterizer, RasterizerArgs&& ... args) {  
        // some fancy iterator that fills on *it = point  
        auto iterator = make_fill_iterator(view);  
        rasterizer(iterator, args...);  
    }  
  
Overloads for circle rasterizers would be useful so it could sort the output by scanlines first, otherwise they destroy cache comprehension.

---

📁 include/boost/gil/rasterization/line.hpp

```diff
  37 |+         const auto target_count = point_count(width, height);
  38 |+         const bool needs_flip = target_count != width;
  39 |+         const double m = [needs_flip, width, height]() {
```

> Username: mloskot  
> Created_at: 2020-08-25 17:03:55 UTC  
> Updated_at: 2021-01-25 17:57:40 UTC  
> Url: https://github.com/boostorg/gil/pull/512#discussion_r476601586  

Nitpick: Please, prefer east-const `bool const`, and `{` in new line for vivid block demarcation

---

📁 include/boost/gil/rasterization/line.hpp

```diff
  29 |+     constexpr std::ptrdiff_t point_count(std::ptrdiff_t width, std::ptrdiff_t height) const noexcept
  30 |+     {
  31 |+         return width > height ? width : height;
```

> Username: mloskot  
> Created_at: 2020-08-25 17:08:11 UTC  
> Updated_at: 2021-01-25 17:57:40 UTC  
> Url: https://github.com/boostorg/gil/pull/512#discussion_r476604016  

Why not `std::max` ?

> Username: simmplecoder  
> Created_at: 2020-08-25 21:26:26 UTC  
> Updated_at: 2021-01-25 17:57:40 UTC  
> Url: https://github.com/boostorg/gil/pull/512#discussion_r476750386  

`std::max` is not constexpr in C++11. I am not sure if it is worth putting the limitation, as one might want to rasterize into C array or `std::array`. Although rasterization itself will not be `constexpr`, knowing the size will allow them to at least use the arrays for storage.

> Username: mloskot  
> Created_at: 2020-08-26 09:26:18 UTC  
> Updated_at: 2021-01-25 17:57:40 UTC  
> Url: https://github.com/boostorg/gil/pull/512#discussion_r477164124  

I missed that fact about C++11 vs C++14. It makes sense. let's leave as it is.

---

📁 include/boost/gil/rasterization/line.hpp

```diff
  32 |+     }
  33 |+ 
  34 |+     template <typename RAIterator>
```

> Username: mloskot  
> Created_at: 2020-08-25 17:09:00 UTC  
> Updated_at: 2021-01-25 17:57:40 UTC  
> Url: https://github.com/boostorg/gil/pull/512#discussion_r476604507  

Can we have less cryptic name for `RAIterator`?


📁 test/core/rasterization/CMakeLists.txt

```diff
   1 |+ # Boost.GIL (Generic Image Library) - tests
   2 |+ #
   3 |+ # Copyright 2019 Miral Shah <miralshah2211@gmail.com>
```

> Username: mloskot  
> Created_at: 2020-08-25 17:28:19 UTC  
> Updated_at: 2021-01-25 17:57:40 UTC  
> Url: https://github.com/boostorg/gil/pull/512#discussion_r476619204  

I'd replace Miral with yourself here, or add yourself.  
  
I know this file may not reach the [certain threshold of originality](https://www.boost.org/users/license.html#FAQ) but it is not terribly essential, and it is part of the rasterization package authored by you.


📁 test/core/rasterization/line.cpp

```diff
   8 |+ namespace gil = boost::gil;
   9 |+ 
  10 |+ void line_equation_following_test(std::ptrdiff_t width, std::ptrdiff_t height)
```

> Username: mloskot  
> Created_at: 2020-08-25 17:32:44 UTC  
> Updated_at: 2021-01-25 17:57:40 UTC  
> Url: https://github.com/boostorg/gil/pull/512#discussion_r476621767  

Why not `test_bresenham_rasterizer_follows_equation`?

---

📁 test/core/rasterization/line.cpp

```diff
  33 |+         }
  34 |+     }
  35 |+ }
```

> Username: mloskot  
> Created_at: 2020-08-25 17:33:20 UTC  
> Updated_at: 2021-01-25 17:57:40 UTC  
> Url: https://github.com/boostorg/gil/pull/512#discussion_r476622128  

The `equation_following_test_cases` seems superfluous to me and this whole body could be moved inside the `main`


📁 test/core/rasterization/circle.cpp

```diff
   7 |+ 
   8 |+ template <typename Rasterizer>
   9 |+ void test_equation_following(std::ptrdiff_t radius, Rasterizer rasterizer)
```

> Username: mloskot  
> Created_at: 2020-08-25 17:34:04 UTC  
> Updated_at: 2021-01-25 17:57:40 UTC  
> Url: https://github.com/boostorg/gil/pull/512#discussion_r476622551  

`test_rasterizer_follows_equation`?


---

## Comment 2

> Username: simmplecoder  
> Created_at: 2020-08-25 21:17:32 UTC  
> Url: https://github.com/boostorg/gil/pull/512#issuecomment-680274687  

@mloskot, TravisCI seems a bit confused due to force push (I accidentally confused diffusion branch with Hough transform). The changes did pass tests on my repo: https://travis-ci.org/github/simmplecoder/gil/builds/721137117

---

## Comment 3

> Username: mloskot  
> Created_at: 2020-08-26 09:32:22 UTC  
> Url: https://github.com/boostorg/gil/pull/512#issuecomment-680770670  

@simmplecoder   
> TravisCI seems a bit confused due to force push   
  
I re-started builds on Travis CI and AzP. I am not able to restart builds on AppVeyor (it's owned by @stefanseefeld and I'm still not a member there :-)). Let's see how the restarted ones go.  
  
You could make a dummy change, a comment or re-format (e.g. east-const cleanup) and push to kickstart rebuild.  
We will squash-merge this PR eventually, so fixup commits are fine.

---

## Comment 4

> Username: mloskot  
> Created_at: 2021-01-24 20:44:48 UTC  
> Url: https://github.com/boostorg/gil/pull/512#issuecomment-766428831  

@simmplecoder Looks like recent merges created some conflicts for this PR. Would you be able to resolve them? If not, I can do it, just let me know. Other than that, I think it's ready to be merged.

---

## Comment 5

> Username: simmplecoder  
> Created_at: 2021-01-25 17:50:31 UTC  
> Updated_at: 2021-01-25 18:01:19 UTC  
> Url: https://github.com/boostorg/gil/pull/512#issuecomment-766996935  

@mloskot  <s>turns out I had some local work I didn't push, I believe I should be able to sort it out tomorrow evening. I'll write back when it is ready.</s>  
  
Turns out I have been mistaken. I rebased off of current develop, shouldn't have problems with merge now.

---

## Review 6 [Approved]

> Username: mloskot  
> Created_at: 2021-01-25 22:29:05 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/gil/pull/512#pullrequestreview-575862086  

Thank you @simmplecoder for your great efforts!

---
