# #314 [gsoc2019] Otsu threshold implemented [Merged]

> Username: miralshah365  
> Created at: 2019-06-21 10:01:59 UTC  
> Updated at: 2020-02-22 13:10:06 UTC  
> Merged at: 2019-07-19 21:40:26 UTC  
> Closed at: 2019-07-19 21:40:26 UTC  
> Url: https://github.com/boostorg/gil/pull/314  

### Description  
New method threshold_optimal introduced. In this method, the user does not need to specify any threshold value but is chosen by the Otsu's algorithm. More details about the algorithm can be found in the reference.  
  
If the image is multi-channel then the algorithm is applied on each channel. If the image has channel depth more than 8bit then each channel is normalized to fit in the range from 0 to 255.  
  
### References  
closes #311  
http://www.labbookpages.co.uk/software/imgProc/otsuThreshold.html  
  
### Tasklist  
  
- [x] Add test case(s)  
- [x] Ensure all CI builds pass  
- [x] Review and approve

---

## Review 1 [Changes requested]

> Username: mloskot  
> Created_at: 2019-06-23 10:58:36 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/gil/pull/314#pullrequestreview-253126033  

@miralshah365 This looks good.  
  
Here is my first batch of comments clearing some trivial aspects of the code.  
  
Could you also try to update your PR against latest `develop` where Olzhas merged his PR,  
and you will need to resolve the conflict in `test/core/image_processing/Jamfile`.  
When you will be updating, please do regular true `git merge` and `git push`,  
but *not* `git rebase` and `git push --force`.  
The issue is that rebase and forced-push update lose tracking of history of review comments.  
  
------  
  
Later today, I will do another round of comments and run proper testing of your code.

📁 include/boost/gil/image_processing/threshold.hpp

```diff
 188 |+ {
 189 |+     //deciding output channel type and creating functor
 190 |+     typedef typename channel_type<SrcView>::type source_channel_t;
```

> Username: mloskot  
> Created_at: 2019-06-23 10:42:51 UTC  
> Updated_at: 2019-07-18 20:58:14 UTC  
> Url: https://github.com/boostorg/gil/pull/314#discussion_r296475169  

Could you prefer to use the `using` declarations?  
  
```  
- typedef typename channel_type<SrcView>::type source_channel_t;  
+ using source_channel_t = typename channel_type<SrcView>::type;  
```

---

📁 include/boost/gil/image_processing/threshold.hpp

```diff
 191 |+     typedef typename channel_type<DstView>::type result_channel_t;
 192 |+ 
 193 |+     std::array<std::size_t, 256> histData{};
```

> Username: mloskot  
> Created_at: 2019-06-23 10:47:18 UTC  
> Updated_at: 2019-07-18 20:58:14 UTC  
> Url: https://github.com/boostorg/gil/pull/314#discussion_r296475348  

@miralshah365 GIL, as well as majority of Boost libraries, follow the C++ standard library naming conventions where the names of variables, function parameters, class data members are all lowercase, with underscores between words.  
  
```  
- histData  
+ histogram   // or histogram_data or hist_data  
```  
  
We need to add a note on that to the `CONRIBUTING.md`.

---

📁 include/boost/gil/image_processing/threshold.hpp

```diff
 192 |+ 
 193 |+     std::array<std::size_t, 256> histData{};
 194 |+     source_channel_t min = std::numeric_limits<source_channel_t>::max(),
```

> Username: mloskot  
> Created_at: 2019-06-23 10:50:01 UTC  
> Updated_at: 2019-07-18 20:58:14 UTC  
> Url: https://github.com/boostorg/gil/pull/314#discussion_r296475439  

Is it names mismatch?  
If it is intentional, there should be a comment explaining why `max` is assigned to variable named `min`.

---

📁 include/boost/gil/image_processing/threshold.hpp

```diff
 193 |+     std::array<std::size_t, 256> histData{};
 194 |+     source_channel_t min = std::numeric_limits<source_channel_t>::max(),
 195 |+         max = std::numeric_limits<source_channel_t>::min();
```

> Username: mloskot  
> Created_at: 2019-06-23 10:51:51 UTC  
> Updated_at: 2019-07-18 20:58:14 UTC  
> Url: https://github.com/boostorg/gil/pull/314#discussion_r296475499  

Since `source_channel_t` is already displayed in `numeric_limits<source_channel_t>`, this is a good place to use `auto`, e.g.:  
  
```  
auto min = std::numeric_limits<source_channel_t>::min()  
auto max = std::numeric_limits<source_channel_t>::max()  
```  
  
(Regarding min to max and max to min assignment, see my previous comment.)

---

📁 include/boost/gil/image_processing/threshold.hpp

```diff
 201 |+         {
 202 |+             typename SrcView::x_iterator src_it = src_view.row_begin(y);
 203 |+ 
```

> Username: mloskot  
> Created_at: 2019-06-23 10:52:53 UTC  
> Updated_at: 2019-07-18 20:58:14 UTC  
> Url: https://github.com/boostorg/gil/pull/314#discussion_r296475541  

Style nitpicking: blank lines seem superfluous here.

---

📁 include/boost/gil/image_processing/threshold.hpp

```diff
 245 |+     //http://www.labbookpages.co.uk/software/imgProc/otsuThreshold.html
 246 |+     //https://www.ipol.im/pub/art/2016/158/
 247 |+ 
```

> Username: mloskot  
> Created_at: 2019-06-23 10:53:23 UTC  
> Updated_at: 2019-07-18 20:58:14 UTC  
> Url: https://github.com/boostorg/gil/pull/314#discussion_r296475554  

Style nitpicking: blank lines seem superfluous here.

---

📁 include/boost/gil/image_processing/threshold.hpp

```diff
 248 |+     std::ptrdiff_t total_pixel = src_view.height() * src_view.width();
 249 |+     std::ptrdiff_t sum = 0, sumB = 0;
 250 |+ 
```

> Username: mloskot  
> Created_at: 2019-06-23 10:53:31 UTC  
> Updated_at: 2019-07-18 20:58:14 UTC  
> Url: https://github.com/boostorg/gil/pull/314#discussion_r296475557  

Style nitpicking: blank lines seem superfluous here.

---

📁 include/boost/gil/image_processing/threshold.hpp

```diff
 250 |+ 
 251 |+     std::size_t wB = 0, wF = 0, threshold = 0;
 252 |+ 
```

> Username: mloskot  
> Created_at: 2019-06-23 10:53:46 UTC  
> Updated_at: 2019-07-18 20:58:14 UTC  
> Url: https://github.com/boostorg/gil/pull/314#discussion_r296475563  

Style nitpicking: blank lines seem superfluous here.  
  
etc.

---

📁 include/boost/gil/image_processing/threshold.hpp

```diff
 258 |+     }
 259 |+ 
 260 |+     for (int t = 0; t < 256; t++) {
```

> Username: mloskot  
> Created_at: 2019-06-23 10:54:11 UTC  
> Updated_at: 2019-07-18 20:58:14 UTC  
> Url: https://github.com/boostorg/gil/pull/314#discussion_r296475577  

```  
- for (int t = 0; t < 256; t++) {  
+ for (int t = 0; t < 256; t++)  
+ {  
```


---

## Review 2 [Changes requested]

> Username: mloskot  
> Created_at: 2019-06-25 20:47:06 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/gil/pull/314#pullrequestreview-254260342  

@miralshah365 Please, "Resolve conversation" for addressed comments.

📁 include/boost/gil/image_processing/threshold.hpp

```diff
  80 |-     typedef typename channel_type<SrcView>::type source_channel_t;
  81 |-     typedef typename channel_type<DstView>::type result_channel_t;
  82 |+     using source_channel_t = channel_type<SrcView>::type;
```

> Username: mloskot  
> Created_at: 2019-06-25 20:46:27 UTC  
> Updated_at: 2019-07-18 20:58:14 UTC  
> Url: https://github.com/boostorg/gil/pull/314#discussion_r297385944  

@miralshah365  The `typename` is still required here, it should read  
  
```  
using source_channel_t = typename channel_type<SrcView>::type;  
```  
  
This should fix the build.


---

## Comment 3

> Username: mloskot  
> Created_at: 2019-06-28 17:40:53 UTC  
> Updated_at: 2019-06-28 17:41:38 UTC  
> Url: https://github.com/boostorg/gil/pull/314#issuecomment-506819033  

@miralshah365 I've started discussion related to review of this PR (and previous #310 feature too),. Please, see the thread [Global binarization vs multi-channel binarization](https://lists.boost.org/boost-gil/2019/06/0253.php)

---

## Review 4 [Changes requested]

> Username: mloskot  
> Created_at: 2019-07-05 21:22:58 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/gil/pull/314#pullrequestreview-258552297  

Review with suggestion for better naming of variables  
- self-descriptive and evident  
- C++ standard library-like, but not of form of any CamelCase

📁 include/boost/gil/image_processing/threshold.hpp

```diff
 249 |+     std::ptrdiff_t sum = 0, sumB = 0;
 250 |+     std::size_t wB = 0, wF = 0, threshold = 0;
 251 |+     double varMax = 0, mB, mF, varBetween;
```

> Username: mloskot  
> Created_at: 2019-07-05 21:22:25 UTC  
> Updated_at: 2019-07-18 20:58:14 UTC  
> Url: https://github.com/boostorg/gil/pull/314#discussion_r300798525  

I'd really like to avoid the cryptic variable names and replace them with self-descriptive alternatives that are longer but also make the comments redundant. For example  
  
```  
sum_total  
sum_back  
weight_back  
weight_fore  
mean_back  
mean_fore  
var_intra_class  
var_within_class  
var_max  
```  
  
```


---

## Review 5 [Approved]

> Username: mloskot  
> Created_at: 2019-07-18 19:49:27 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/gil/pull/314#pullrequestreview-263871575  

@miralshah365 LGTM! Feel free to merge.  
  
Please, consider adding a test case with `gray8_image_t` (in separate PR).

---
