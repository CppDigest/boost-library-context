# #477 Fix image constructor from other image [Merged]

> Username: sdebionne  
> Created at: 2020-04-08 08:03:37 UTC  
> Updated at: 2020-07-11 18:03:46 UTC  
> Merged at: 2020-04-11 11:21:22 UTC  
> Closed at: 2020-04-11 11:21:22 UTC  
> Url: https://github.com/boostorg/gil/pull/477  

<!-- Pull Requests MUST come from topic branch based on develop, and NEVER on `master) --->  
  
### Description  
  
Fixes  
  
```c++  
template <typename P2, bool IP2, typename Alloc2>  
image(const image<P2,IP2,Alloc2>& img)  
```  
  
giving  
  
```  
include/boost/gil/image.hpp:108:84: error: 'std::size_t boost::gil::image<boost::gil::pixel<unsigned char, boost::gil  
::layout<boost::mp11::mp_list<boost::gil::gray_color_t> > >, false, std::allocator<unsigned char> >::_align_in_bytes' is  
 private within this context  
     image(const image<P2,IP2,Alloc2>& img) : _memory(nullptr), _align_in_bytes(img._align_in_bytes), _alloc(img._alloc)  
                                                                                ~~~~^~~~~~~~~~~~~~~  
../include/boost/gil/image.hpp:345:20: note: declared private here  
     std::size_t    _align_in_bytes;  
```  
  
### References  
  
<!-- Any links related to this PR: issues, other PRs, mailing list threads, StackOverflow questions, etc. -->  
  
- Raised on [Slack](https://cpplang.slack.com/archives/CSVT0STV2/p1586328948062100).  
- Work on this revealed #478 issue  
  
### Tasklist  
  
<!-- Add YOUR OWN TASK(s), especially if your PR is a work in progress -->  
  
- [x] Add test case(s)  
- [x] Ensure all CI builds pass  
- [x] Review and approve

---

## Review 1 [Commented]

> Username: mloskot  
> Created_at: 2020-04-08 21:25:19 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/gil/pull/477#pullrequestreview-390343512  

📁 test/core/image/image.cpp

```diff
  64 |+         //     auto v1 = gil::const_view(image1);
  65 |+         //     auto v2 = gil::const_view(image2);
  66 |+         //     BOOST_TEST_ALL_EQ(v1.begin(), v1.end(), v2.begin(), v2.end());
```

> Username: mloskot  
> Created_at: 2020-04-08 21:25:18 UTC  
> Updated_at: 2020-04-09 07:07:52 UTC  
> Url: https://github.com/boostorg/gil/pull/477#discussion_r405824402  

I documented this issue in https://github.com/boostorg/gil/issues/478

> Username: sdebionne  
> Created_at: 2020-04-09 06:30:59 UTC  
> Updated_at: 2020-04-09 07:07:52 UTC  
> Url: https://github.com/boostorg/gil/pull/477#discussion_r405983075  

I saw the very thoroughly documented issue, thanks!


---

## Review 2 [Commented]

> Username: mloskot  
> Created_at: 2020-04-08 21:26:45 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/gil/pull/477#pullrequestreview-390344326  

📁 include/boost/gil/image.hpp

```diff
 340 |     view_t       _view;      // contains pointer to the pixels, the image size and ways to navigate pixels
 341 |+     
 342 |+     template <typename P2, bool IP2, typename Alloc2> friend class image; // for construction from other type
```

> Username: mloskot  
> Created_at: 2020-04-08 21:26:45 UTC  
> Updated_at: 2020-04-09 07:07:52 UTC  
> Url: https://github.com/boostorg/gil/pull/477#discussion_r405825056  

Please, keep (new) lines up to 100

> Username: sdebionne  
> Created_at: 2020-04-09 06:29:57 UTC  
> Updated_at: 2020-04-09 07:07:52 UTC  
> Url: https://github.com/boostorg/gil/pull/477#discussion_r405982733  

OK let me fix that.


---

## Review 3 [Commented]

> Username: mloskot  
> Created_at: 2020-04-08 21:27:27 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/gil/pull/477#pullrequestreview-390344699  

📁 test/core/image/test_fixture.hpp

```diff
  39 | >;
  40 | 
  41 |+ using multiband_image_types = std::tuple
```

> Username: mloskot  
> Created_at: 2020-04-08 21:27:27 UTC  
> Updated_at: 2020-04-09 07:07:52 UTC  
> Url: https://github.com/boostorg/gil/pull/477#discussion_r405825374  

Nitpick: I'd name it `interleaved_image_types` or `rgb_interleaved_image_types`

> Username: sdebionne  
> Created_at: 2020-04-09 06:29:40 UTC  
> Updated_at: 2020-04-09 07:07:52 UTC  
> Url: https://github.com/boostorg/gil/pull/477#discussion_r405982632  

Well multiband is for the opposite of grayscale (single band). Interleaved is rather opposite of planar. `rgb` is a specialization of mutliband. Makes sense?  
`gil::gray8_image_t img1(gil::gray8_planar_image_t{}); ` is not implemented (fails to compile) because `struct homogeneous_color_base<Element, Layout, 1>` does not have the full implementation that the other specializations for multibands have. Not sure why. Probably because single band planar image is useless?

> Username: sdebionne  
> Created_at: 2020-04-09 07:03:52 UTC  
> Updated_at: 2020-04-09 07:07:52 UTC  
> Url: https://github.com/boostorg/gil/pull/477#discussion_r405996660  

I changed for `rgb_interleaved_image_types`.

> Username: mloskot  
> Created_at: 2020-04-10 12:17:39 UTC  
> Updated_at: 2020-04-10 12:17:40 UTC  
> Url: https://github.com/boostorg/gil/pull/477#discussion_r406732330  

> Well multiband is for the opposite of grayscale (single band). Interleaved is rather opposite of planar. rgb is a specialization of mutliband. Makes sense?  
  
Yes, that is how I understand it too.  
  
> Probably because single band planar image is useless?  
  
Conceptually, single band planar should be equivalent to interleaved grayscale,  
  
Details of the issues are collected in #478


---

## Review 4 [Approved]

> Username: mloskot  
> Created_at: 2020-04-08 21:30:58 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/gil/pull/477#pullrequestreview-390346798  

@sdebionne LGTM, thanks!  
  
I made several minor comments, not requests for changes though.

---

## Review 5 [Approved]

> Username: mloskot  
> Created_at: 2020-04-09 10:44:00 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/gil/pull/477#pullrequestreview-390683758  

Thank you

---

## Comment 6

> Username: mloskot  
> Created_at: 2020-04-09 10:48:00 UTC  
> Updated_at: 2020-04-09 10:48:14 UTC  
> Url: https://github.com/boostorg/gil/pull/477#issuecomment-611462662  

Let's wait for the CI builds.  
  
I'm not sure if it will make it to Boost 1.73, let's see.

---
