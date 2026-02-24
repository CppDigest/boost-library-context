# #522 Fix conversion from RGB to signed CMYK [Merged]

> Username: harshitpant1  
> Created at: 2020-10-08 16:27:33 UTC  
> Updated at: 2021-01-22 17:00:13 UTC  
> Merged at: 2020-10-10 13:17:48 UTC  
> Closed at: 2020-10-10 13:17:48 UTC  
> Url: https://github.com/boostorg/gil/pull/522  

### Description  
  
Conversion of RGB pixels based on unsigned integral channels to CMYK based on signed integral channels was/is broken (issue #479). It happend because relations used for color correction like `c' = (c - k) * s` etc. didn't work well for signed integral channels.   
  
This correction is attempted by doing **all color correction calculations of pixel values based on unsigned** integral channels then(after the calculations are finished) converting those values to signed ones.  
  
### References  
  
Principles of Digital Image Processing - Fundamental Techniques (Book name provided in `color_convert.hpp`)  
  
### Tasklist  
  
- [x] Add test case(s)  
- [x] Ensure all CI builds pass  
- [ ] Review and approve

---

## Review 1 [Changes requested]

> Username: mloskot  
> Created_at: 2020-10-08 16:34:54 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/gil/pull/522#pullrequestreview-504964716  

Thank you for the attempt to fix it.  
  
Please, if you could [DO ensure any new features or changes to existing behaviours are covered with test cases](https://github.com/boostorg/gil/blob/develop/CONTRIBUTING.md#pull-requests). IOW, please add a tests for the bug fix (see some samples in #479).

📁 include/boost/gil/color_convert.hpp

```diff
 160 |+         src_t const r  = get_color(src,red_t());
 161 |+         src_t const g  = get_color(src,green_t());
 162 |+         src_t const b  = get_color(src,blue_t());
```

> Username: mloskot  
> Created_at: 2020-10-08 16:33:47 UTC  
> Updated_at: 2020-10-10 10:51:42 UTC  
> Url: https://github.com/boostorg/gil/pull/522#discussion_r501857056  

Please, avoid any stylistic or formatting changes in the existing code as in [DO NOT mix independent, unrelated changes in one PR](https://github.com/boostorg/gil/blob/develop/CONTRIBUTING.md#pull-requests)

> Username: harshitpant1  
> Created_at: 2020-10-08 17:17:39 UTC  
> Updated_at: 2020-10-10 10:51:42 UTC  
> Url: https://github.com/boostorg/gil/pull/522#discussion_r501883815  

Ok I'll work on adding tests.  
Also I would be thankful if you provide me some link for doing the same.

> Username: mloskot  
> Created_at: 2020-10-08 17:33:19 UTC  
> Updated_at: 2020-10-10 10:51:42 UTC  
> Url: https://github.com/boostorg/gil/pull/522#discussion_r501893443  

As a first stab at tests, you can just add it as a new program, e.g. `gil/test/core/color/default_color_converter_rgb_to_cmyk.cpp`, based on my sample in #479.  
  
Eventually, we may aggregate it into this [test/core/color/default_color_converter_impl.cpp](https://github.com/boostorg/gil/blob/01d95998c278d113af32ed34ba7d1ccc5987e4be/test/core/color/default_color_converter_impl.cpp).  
  
By the way, the `test/core/color/default_color_converter_impl.cpp` is minimal and it would be good to cover wider range(s) of test values.

> Username: harshitpant1  
> Created_at: 2020-10-08 17:35:10 UTC  
> Updated_at: 2020-10-10 10:51:42 UTC  
> Url: https://github.com/boostorg/gil/pull/522#discussion_r501894630  

Ok, thanks.

> Username: mloskot  
> Created_at: 2020-10-08 17:41:41 UTC  
> Updated_at: 2020-10-10 10:51:42 UTC  
> Url: https://github.com/boostorg/gil/pull/522#discussion_r501898707  

My note on  `test/core/color/default_color_converter_impl.cpp` being minimal is just in case you wondered, why it does not cover wider range of values. I'd be happy if it did -  there is still plenty of work to do in GIL tests (help is highly welcome!).  
  
So, I encourage you to consider writing better tests for this fix than what we've got in `test/core/color/default_color_converter_impl.cpp` so far :)

> Username: harshitpant1  
> Created_at: 2020-10-08 17:53:40 UTC  
> Updated_at: 2020-10-10 10:51:42 UTC  
> Url: https://github.com/boostorg/gil/pull/522#discussion_r501906501  

That would be exciting to do, thanks for that.  
I would get to learn new things.

> Username: mloskot  
> Created_at: 2020-10-08 17:54:59 UTC  
> Updated_at: 2020-10-10 10:51:42 UTC  
> Url: https://github.com/boostorg/gil/pull/522#discussion_r501907382  

👍 Warning: it's not easy, it's sometimes boring or frustrating. I'll try to help :)

> Username: harshitpant1  
> Created_at: 2020-10-08 17:56:07 UTC  
> Updated_at: 2020-10-10 10:51:42 UTC  
> Url: https://github.com/boostorg/gil/pull/522#discussion_r501908107  

Ok, thanks.

---

📁 include/boost/gil/color_convert.hpp

```diff
 169 |-         dst_t const y = channel_invert(channel_convert<dst_t>(b)); // y = 1 - b
 170 |-         dst_t const k = (std::min)(c, (std::min)(m, y));           // k = minimum(c, m, y)
 164 |+         using dst_us_t = typename channel_type< cmyk8_pixel_t >::type;
```

> Username: mloskot  
> Created_at: 2020-10-08 16:34:24 UTC  
> Updated_at: 2020-10-10 10:51:42 UTC  
> Url: https://github.com/boostorg/gil/pull/522#discussion_r501857475  

`us` in `dst_us_t` stands for?

> Username: harshitpant1  
> Created_at: 2020-10-08 16:46:41 UTC  
> Updated_at: 2020-10-10 10:51:42 UTC  
> Url: https://github.com/boostorg/gil/pull/522#discussion_r501865020  

us - unsigned

> Username: mloskot  
> Created_at: 2020-10-08 17:27:24 UTC  
> Updated_at: 2020-10-10 10:51:42 UTC  
> Url: https://github.com/boostorg/gil/pull/522#discussion_r501889649  

Well, I don't think it's a self-descriptive naming.  
  
  
Moreover, `dst` does no longer fit in that new context as the `dst_us_t` is a (unsigned) type of an intermediate calculation value, and I'd make the intention explicit:  
  
```  
using uint_t = typename channel_type<cmyk8_pixel_t>::type;  
```  
  
By the way, in GIL, `cmyk8_*` is based on unsigned and `cmyk8s_*` is signed.

> Username: harshitpant1  
> Created_at: 2020-10-08 17:39:34 UTC  
> Updated_at: 2020-10-10 10:51:42 UTC  
> Url: https://github.com/boostorg/gil/pull/522#discussion_r501897334  

Understood, thanks.  
  
(also I assume I will have to make some changes to this pull request, and not create another one. )

> Username: mloskot  
> Created_at: 2020-10-08 18:04:36 UTC  
> Updated_at: 2020-10-10 10:51:42 UTC  
> Url: https://github.com/boostorg/gil/pull/522#discussion_r501913538  

Yes, anything related to this fix should be added to this PR.  
Simply, add more commits to your `bugfix/fixed-conversion-of-rgb-to-signed-cmyk-issue#479` branch and just `git push` to your fork (no need to `git push --force`). We will squash this PR before merging, so any fixup and intermediate commits will disappear.

> Username: harshitpant1  
> Created_at: 2020-10-08 18:08:23 UTC  
> Updated_at: 2020-10-10 10:51:42 UTC  
> Url: https://github.com/boostorg/gil/pull/522#discussion_r501915870  

Ok, understood.  
Thanks again.


---

## Comment 2

> Username: harshitpant1  
> Created_at: 2020-10-09 09:28:10 UTC  
> Url: https://github.com/boostorg/gil/pull/522#issuecomment-706074911  

@mloskot  can you please have a look at the tests I just added.

---

## Review 3 [Changes requested]

> Username: mloskot  
> Created_at: 2020-10-09 18:35:22 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/gil/pull/522#pullrequestreview-505895585  

Good work with the tests!  
  
I added two nitpick comments.  
  
We also need to update the build configuration, so we can build and run the test program locally and with the the CI builds, so if you could do this:  
  
For Boost.Build, add `run default_color_converter_rgb_to_cmyk.cpp  ;` below this line  
  
https://github.com/boostorg/gil/blob/f6a35532f05963804c83f6519955c6fd283a3978/test/core/color/Jamfile#L15  
  
For CMake, remove `)` from this line and add `default_color_converter_rgb_to_cmyk)` below this line:  
  
https://github.com/boostorg/gil/blob/f6a35532f05963804c83f6519955c6fd283a3978/test/core/color/CMakeLists.txt#L11

📁 include/boost/gil/color_convert.hpp

```diff
 170 |-         dst_t const k = (std::min)(c, (std::min)(m, y));           // k = minimum(c, m, y)
 164 |+         using uint_t = typename channel_type<cmyk8_pixel_t>::type;
 165 |+         uint_t c = channel_invert(channel_convert<uint_t>(r)); // c = 1 - r
```

> Username: mloskot  
> Created_at: 2020-10-09 18:17:26 UTC  
> Updated_at: 2020-10-10 10:51:42 UTC  
> Url: https://github.com/boostorg/gil/pull/522#discussion_r502598832  

Nitpick: trailing space after `r` should be removed

---

📁 include/boost/gil/color_convert.hpp

```diff
 166 |+         uint_t m = channel_invert(channel_convert<uint_t>(g)); // m = 1 - g
 167 |+         uint_t y = channel_invert(channel_convert<uint_t>(b)); // y = 1 - b
 168 |+         uint_t k = (std::min)(c,(std::min)(m,y));              //k = minimum(c, m, y)
```

> Username: mloskot  
> Created_at: 2020-10-09 18:17:43 UTC  
> Updated_at: 2020-10-10 10:51:42 UTC  
> Url: https://github.com/boostorg/gil/pull/522#discussion_r502598957  

Nitpick: missing space before `k` in `//k`


---

## Comment 4

> Username: mloskot  
> Created_at: 2020-10-10 09:43:56 UTC  
> Updated_at: 2020-10-10 09:44:17 UTC  
> Url: https://github.com/boostorg/gil/pull/522#issuecomment-706521335  

The CI are failing because of  
  
```  
libs\gil\test\core\color\default_color_converter_rgb_to_cmyk.cpp(10):  
  fatal error C1083: Cannot open include file: 'test_fixture.hpp':  
  No such file or directory  
```  
  
I think you can remove include of this header from the test, as you don't seem to be using any fixture.

---

## Comment 5

> Username: harshitpant1  
> Created_at: 2020-10-10 11:56:56 UTC  
> Url: https://github.com/boostorg/gil/pull/522#issuecomment-706537760  

I guess I should have been more attentive and avoided that issue/fail.  
The CI checks are successful now.  
Thanks for your help.

---

## Comment 6

> Username: mloskot  
> Created_at: 2020-10-10 13:14:52 UTC  
> Updated_at: 2020-10-10 13:16:02 UTC  
> Url: https://github.com/boostorg/gil/pull/522#issuecomment-706547654  

We all learn from mistakes. As you see, submitting a PR is an effort.  
  
I'm on mobile now and can't verify the result, but I'll take it from the CI, and I'll merge.   
  
Thank you!

---

## Comment 7

> Username: harshitpant1  
> Created_at: 2020-10-10 16:00:49 UTC  
> Url: https://github.com/boostorg/gil/pull/522#issuecomment-706570777  

Thank you as well.

---
