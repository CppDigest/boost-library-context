# #342 Filter generation [Merged]

> Username: simmplecoder  
> Created at: 2019-07-21 01:41:59 UTC  
> Updated at: 2020-08-01 20:09:34 UTC  
> Merged at: 2019-07-23 00:14:03 UTC  
> Closed at: 2019-07-23 00:14:03 UTC  
> Url: https://github.com/boostorg/gil/pull/342  

### Description  
  
Add generators for box (normalized and unnormalized) kernel and Gaussian kernel.  
  
### Environment  
  
Anything compatible with Boost 1.70  
  
### References  
  
https://en.wikipedia.org/wiki/Gaussian_blur  
  
https://en.wikipedia.org/wiki/Box_blur  
  
### Tasklist  
  
- [X] Implement generators  
- [X] Add test case(s)  
- [X] Fix precision problem (not completely, but specify error margin in percents)  
- [x] Ensure all CI builds pass  
- [x] Review and approve

---

## Review 1 [Commented]

> Username: mloskot  
> Created_at: 2019-07-21 10:29:29 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/gil/pull/342#pullrequestreview-264528946  

I'll leave the actual review to @stefanseefeld, so this are just general comments.  
  
I'm interested to understand _what's the motivation_ of using `gray32f_view_t` as a kernel type instead of Numeric's types:  
  
https://github.com/boostorg/gil/blob/ab4c686970e64db978f4fcd4f6d1c20e11f605eb/include/boost/gil/extension/numeric/kernel.hpp#L77-L78  
  
https://github.com/boostorg/gil/blob/ab4c686970e64db978f4fcd4f6d1c20e11f605eb/include/boost/gil/extension/numeric/kernel.hpp#L92-L93  
  
If the proposed generators use the `kernel_1d*` types, then produced kernels are directly usable with the Numeric's `convolve` functions.  
  
AFAIK, @stefanseefeld is going to move Numeric extension features to the core what will make the kernel types available.

📁 include/boost/gil/image_processing/numeric.hpp

```diff
  57 |+ /// in which all entries will be equal to
  58 |+ /// 1 / (dst.size())
  59 |+ void generate_normalized_mean(boost::gil::gray32f_view_t dst) {
```

> Username: mloskot  
> Created_at: 2019-07-21 10:01:10 UTC  
> Updated_at: 2019-07-22 21:43:45 UTC  
> Url: https://github.com/boostorg/gil/pull/342#discussion_r305610060  

Missing `inline`.  
  
The full qualification w/ namespace `boost::gil::gray32f_view_t` is not required, or is it?

> Username: simmplecoder  
> Created_at: 2019-07-21 17:36:52 UTC  
> Updated_at: 2019-07-22 21:43:45 UTC  
> Url: https://github.com/boostorg/gil/pull/342#discussion_r305628303  

It's just that I'm paranoid about ADL, even though they apply only to function calls. I will deal with the inlines shortly, thanks for catching that.

---

📁 include/boost/gil/image_processing/numeric.hpp

```diff
  56 |+ /// Fills supplied view with normalized mean
  57 |+ /// in which all entries will be equal to
  58 |+ /// 1 / (dst.size())
```

> Username: mloskot  
> Created_at: 2019-07-21 10:16:17 UTC  
> Updated_at: 2019-07-22 21:43:45 UTC  
> Url: https://github.com/boostorg/gil/pull/342#discussion_r305610566  

Since this is Doxygen comment, I'd suggest to mark this formula as code example using either Markdown which is supported  
http://www.doxygen.nl/manual/markdown.html#md_codespan  
or `\code` which I guess should also work in single line `\code ... \endcode`  
http://www.doxygen.nl/manual/commands.html#cmdcode

---

📁 include/boost/gil/image_processing/numeric.hpp

```diff
  93 |+     for (boost::gil::gray32f_view_t::coord_t y = 0; y < dst.height(); ++y)
  94 |+     {
  95 |+         for (boost::gil::gray32f_view_t::coord_t x = 0; x < dst.width(); ++x)
```

> Username: mloskot  
> Created_at: 2019-07-21 10:17:49 UTC  
> Updated_at: 2019-07-22 21:43:45 UTC  
> Url: https://github.com/boostorg/gil/pull/342#discussion_r305610631  

`boost::gil::gray32f_view_t::coord_t` is fine, but `std::ptrdiff_t` is simpler :-)

> Username: simmplecoder  
> Created_at: 2019-07-21 17:37:26 UTC  
> Updated_at: 2019-07-22 21:43:45 UTC  
> Url: https://github.com/boostorg/gil/pull/342#discussion_r305628331  

I just don't want to get hit by another type change :)

> Username: mloskot  
> Created_at: 2019-07-21 18:00:31 UTC  
> Updated_at: 2019-07-22 21:43:45 UTC  
> Url: https://github.com/boostorg/gil/pull/342#discussion_r305629289  

`width()` and `height()` is guaranteed to be `ptrdiff_t`


📁 test/core/image_processing/simple_kernels.cpp

```diff
   7 |+ namespace gil = boost::gil;
   8 |+ 
   9 |+ bool are_equal(gil::gray32f_view_t expected, gil::gray32f_view_t actual)
```

> Username: mloskot  
> Created_at: 2019-07-21 10:17:13 UTC  
> Updated_at: 2019-07-22 21:43:45 UTC  
> Url: https://github.com/boostorg/gil/pull/342#discussion_r305610589  

I know this is not grammatically sound, but should read `is_equal`  like in `is_same<T1, T2>` :)

> Username: simmplecoder  
> Created_at: 2019-07-21 20:56:17 UTC  
> Updated_at: 2019-07-22 21:43:45 UTC  
> Url: https://github.com/boostorg/gil/pull/342#discussion_r305635577  

It turns out the function is not even used, so I removed it

---

📁 test/core/image_processing/simple_kernels.cpp

```diff
  12 |+         return false;
  13 |+ 
  14 |+     for (long int y = 0; y < expected.height(); ++y)
```

> Username: mloskot  
> Created_at: 2019-07-21 10:19:40 UTC  
> Updated_at: 2019-07-22 21:43:45 UTC  
> Url: https://github.com/boostorg/gil/pull/342#discussion_r305610699  

I'll be a stubborn old man about `std::ptrdiff_t` fits here better than `long int`  :)


---

## Review 2 [Commented]

> Username: mloskot  
> Created_at: 2019-07-21 10:32:43 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/gil/pull/342#pullrequestreview-264529993  

📁 include/boost/gil/image_processing/numeric.hpp

```diff
  71 |+ ///
  72 |+ /// Fills supplied view with 1s (ones)
  73 |+ void generate_unnormalized_mean(boost::gil::gray32f_view_t dst) {
```

> Username: mloskot  
> Created_at: 2019-07-21 10:32:43 UTC  
> Updated_at: 2019-07-22 21:43:45 UTC  
> Url: https://github.com/boostorg/gil/pull/342#discussion_r305611131  

Missing `inline`.


---

## Review 3 [Commented]

> Username: mloskot  
> Created_at: 2019-07-21 10:32:56 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/gil/pull/342#pullrequestreview-264530004  

📁 include/boost/gil/image_processing/numeric.hpp

```diff
  85 |+ /// Fills supplied view with values taken from Gaussian distribution. See
  86 |+ /// https://en.wikipedia.org/wiki/Gaussian_blur
  87 |+ void generate_gaussian_kernel(boost::gil::gray32f_view_t dst, double sigma) {
```

> Username: mloskot  
> Created_at: 2019-07-21 10:32:55 UTC  
> Updated_at: 2019-07-22 21:43:45 UTC  
> Url: https://github.com/boostorg/gil/pull/342#discussion_r305611139  

Missing `inline`.


---
