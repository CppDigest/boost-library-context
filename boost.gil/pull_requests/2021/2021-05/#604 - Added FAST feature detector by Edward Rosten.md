# #604 Added FAST feature detector by Edward Rosten [Open]

> Username: Sayan-Chaudhuri  
> Created at: 2021-05-01 16:59:22 UTC  
> Updated at: 2022-06-25 17:02:27 UTC  
> Url: https://github.com/boostorg/gil/pull/604  

<!-- Pull Requests MUST come from topic branch based on develop, and NEVER on `master) --->  
  
### Description  
This pull request adds the FAST feature detector which is used for corner detection in real time scenarios   
<!-- What does this pull request do? -->  
  
1. Fusing points and lines for high performance tracking.  
2. Machine learning for high-speed corner detection.  
3. Faster and better: A machine learning approach to corner detection  
  
### References  
  
<!-- Any links related to this PR: issues, other PRs, mailing list threads, StackOverflow questions, etc. -->  
  
https://www.edwardrosten.com/work/fast.html  
  
### Tasklist  
  
<!-- Add YOUR OWN TASK(s), especially if your PR is a work in progress -->  
  
- [x] Add test case(s)  
- [ ] Ensure all CI builds pass  
- [ ] Review and approve

---

## Comment 1

> Username: Sayan-Chaudhuri  
> Created_at: 2021-05-01 17:02:55 UTC  
> Url: https://github.com/boostorg/gil/pull/604#issuecomment-830662435  

Would love to have feedback from @lpranam @mloskot @codejaeger  if you find time in your busy schedule to review this PR.  
I have tried to maintain the following things based on feedback in earlier pull requests.  
1.Code is short  
2.Consistent formatting for improved readability  
3.Did not mix upper and lower case in file names

---

## Review 2 [Changes requested]

> Username: lpranam  
> Created_at: 2021-05-02 00:02:11 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/gil/pull/604#pullrequestreview-649828351  

First Thank you for your time and contributions. I am yet to have a look into the actual algorithm had a glace and found problems with code.  
  
1. file name fast does not make any sense in absolute context  
2. we do not use camel case anywhere in the naming  
3. spaces and newlines are inconsistent  
4. template parameter names are single later which makes it hard to understand and impossible to review  
  
maybe try formatting code with clang-format from #596 or with the clang-format provided in the example. Most of the styling problem will be solved with it just have to take care of the naming conventions. You may wanna have a look into code base for names

---

## Comment 3

> Username: Sayan-Chaudhuri  
> Created_at: 2021-05-02 18:05:20 UTC  
> Url: https://github.com/boostorg/gil/pull/604#issuecomment-830847815  

@lpranam I have tried to follow your advice and made the changes .I would request you and @mloskot @codejaeger  to kindly review it if you find time amidst your busy schedule.

---

## Comment 4

> Username: Sayan-Chaudhuri  
> Created_at: 2021-05-02 18:06:03 UTC  
> Url: https://github.com/boostorg/gil/pull/604#issuecomment-830847926  

The number of code lines increased due to formatting

---

## Comment 5

> Username: lpranam  
> Created_at: 2021-05-02 18:44:25 UTC  
> Url: https://github.com/boostorg/gil/pull/604#issuecomment-830853319  

@Sayan-Chaudhuri no one cares about number of lines increases due to adding necessary spaces and formatting. when in past I said that no one would require 500K lines that was because it 500K lines of code no one counts while space. White space is needed.

---

## Comment 6

> Username: Sayan-Chaudhuri  
> Created_at: 2021-05-02 19:29:05 UTC  
> Url: https://github.com/boostorg/gil/pull/604#issuecomment-830859310  

Actually I mentioned it because initially I was very happy to keep the code concise and readable. But then the lines increased and so I felt bad.. :) .

---

## Comment 7

> Username: mloskot  
> Created_at: 2021-05-02 19:41:21 UTC  
> Url: https://github.com/boostorg/gil/pull/604#issuecomment-830860805  

@Sayan-Chaudhuri I believe we have discussed the initial issues pointed out by @lpranam above during review of https://github.com/boostorg/gil/pull/597#pullrequestreview-639138013  
  
Please, try to avoid similar issues over and over again. This will save time to us all!

---

## Review 8 [Changes requested]

> Username: mloskot  
> Created_at: 2021-05-02 19:54:29 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/gil/pull/604#pullrequestreview-649946286  

📁 include/boost/gil/image_processing/fast_feature_detector.hpp

```diff
   9 |+ #include <cmath>
  10 |+ #include <vector>
  11 |+ namespace boost { namespace gil {
```

> Username: mloskot  
> Created_at: 2021-05-02 19:43:48 UTC  
> Updated_at: 2021-05-08 07:31:42 UTC  
> Url: https://github.com/boostorg/gil/pull/604#discussion_r624745147  

Blank line is missing

> Username: Sayan-Chaudhuri  
> Created_at: 2021-05-03 15:01:53 UTC  
> Updated_at: 2021-05-08 07:31:42 UTC  
> Url: https://github.com/boostorg/gil/pull/604#discussion_r625149387  

Done.

---

📁 include/boost/gil/image_processing/fast_feature_detector.hpp

```diff
  11 |+ namespace boost { namespace gil {
  12 |+ namespace detail {
  13 |+ template <typename srcview>
```

> Username: mloskot  
> Created_at: 2021-05-02 19:43:58 UTC  
> Updated_at: 2021-05-08 07:31:42 UTC  
> Url: https://github.com/boostorg/gil/pull/604#discussion_r624745165  

- blank line missing  
- `srcview` is template parameter - template parameters must read CamelCase so `SrcView`

> Username: Sayan-Chaudhuri  
> Created_at: 2021-05-03 15:02:28 UTC  
> Updated_at: 2021-05-08 07:31:42 UTC  
> Url: https://github.com/boostorg/gil/pull/604#discussion_r625149825  

Inserted Blank lines, Camelcase used

---

📁 include/boost/gil/image_processing/fast_feature_detector.hpp

```diff
  12 |+ namespace detail {
  13 |+ template <typename srcview>
  14 |+ bool fast_feature_detector(const srcview& buffer, int r, int c, std::vector<point_t>& points, int t)
```

> Username: mloskot  
> Created_at: 2021-05-02 19:46:03 UTC  
> Updated_at: 2021-05-08 07:31:42 UTC  
> Url: https://github.com/boostorg/gil/pull/604#discussion_r624745376  

I recommend "eastconst": `SrcView const& buffer`  
https://mariusbancila.ro/blog/2018/11/23/join-the-east-const-revolution/

> Username: Sayan-Chaudhuri  
> Created_at: 2021-05-03 15:02:45 UTC  
> Updated_at: 2021-05-08 07:31:42 UTC  
> Url: https://github.com/boostorg/gil/pull/604#discussion_r625150033  

Done.

---

📁 include/boost/gil/image_processing/fast_feature_detector.hpp

```diff
  14 |+ bool fast_feature_detector(const srcview& buffer, int r, int c, std::vector<point_t>& points, int t)
  15 |+ {
  16 |+     int valid_points_count = 16;
```

> Username: mloskot  
> Created_at: 2021-05-02 19:46:21 UTC  
> Updated_at: 2021-05-08 07:31:42 UTC  
> Url: https://github.com/boostorg/gil/pull/604#discussion_r624745405  

`int const valid_points_count`

> Username: Sayan-Chaudhuri  
> Created_at: 2021-05-03 15:03:01 UTC  
> Updated_at: 2021-05-08 07:31:42 UTC  
> Url: https://github.com/boostorg/gil/pull/604#discussion_r625150217  

removed the variable altogether

---

📁 include/boost/gil/image_processing/fast_feature_detector.hpp

```diff
  16 |+     int valid_points_count = 16;
  17 |+     auto src_loc = buffer.xy_at(c, r);
  18 |+     std::vector<int> threshold_indicator, intensity_array(16);
```

> Username: mloskot  
> Created_at: 2021-05-02 19:46:47 UTC  
> Updated_at: 2021-05-08 07:31:42 UTC  
> Url: https://github.com/boostorg/gil/pull/604#discussion_r624745449  

One variable per line please.

> Username: Sayan-Chaudhuri  
> Created_at: 2021-05-03 15:25:50 UTC  
> Updated_at: 2021-05-08 07:31:42 UTC  
> Url: https://github.com/boostorg/gil/pull/604#discussion_r625166223  

done

---

📁 include/boost/gil/image_processing/fast_feature_detector.hpp

```diff
  19 |+     std::vector<decltype(src_loc.cache_location(0, -1))> pointers(valid_points_count);
  20 |+     //stroring intensities of pixels on circumference beforehand to decrease runtime
  21 |+     for (int i = 0; i < 16; i++)
```

> Username: mloskot  
> Created_at: 2021-05-02 19:48:20 UTC  
> Updated_at: 2021-05-08 07:31:42 UTC  
> Url: https://github.com/boostorg/gil/pull/604#discussion_r624745638  

Isn't `std::size_t` a better choice here?

> Username: Sayan-Chaudhuri  
> Created_at: 2021-05-03 15:24:08 UTC  
> Updated_at: 2021-05-08 07:31:42 UTC  
> Url: https://github.com/boostorg/gil/pull/604#discussion_r625165074  

used std::size_t,std::ptrdiff_t wherever possible, size_t being unsigned in nature cannot be used for threshold_indicator

---

📁 include/boost/gil/image_processing/fast_feature_detector.hpp

```diff
  73 |+ std::ptrdiff_t
  74 |+     calculate_score(srcview& src, int i, int j, std::vector<point_t>& points, int threshold)
  75 |+ {
```

> Username: mloskot  
> Created_at: 2021-05-02 19:50:09 UTC  
> Updated_at: 2021-05-08 07:31:42 UTC  
> Url: https://github.com/boostorg/gil/pull/604#discussion_r624745906  

Is this formatting with indented `    calculate_score` by the `clang-format`? /cc @lpranam

> Username: mloskot  
> Created_at: 2021-05-06 18:16:28 UTC  
> Updated_at: 2021-05-08 07:31:42 UTC  
> Url: https://github.com/boostorg/gil/pull/604#discussion_r627660687  

@lpranam  I will answer myself, no, it is not `clang-format` output.  
  
Your `.clang-format` generates expected output:  
  
```  
template <typename SrcView>  
std::size_t calculate_score(  
    SrcView const& src,  
    std::size_t i,  
    std::size_t j,  
    std::vector<point_t>& points,  
    std::size_t threshold)  
{  
````

> Username: Sayan-Chaudhuri  
> Created_at: 2021-05-06 18:35:57 UTC  
> Updated_at: 2021-05-08 07:31:42 UTC  
> Url: https://github.com/boostorg/gil/pull/604#discussion_r627673007  

I used the .clang-format file given by @lpranam  in the above links. But i was able to install clang-13 and not 12. May be that's the reason. Going to look into this matter again and update. Thanks for verifying @mloskot .

> Username: mloskot  
> Created_at: 2021-05-06 20:19:42 UTC  
> Updated_at: 2021-05-08 07:31:42 UTC  
> Url: https://github.com/boostorg/gil/pull/604#discussion_r627736735  

I'd suggest to use clang-format 12 but  
I'd also expect clang-format 13 to give output close to clang-format 12

> Username: Sayan-Chaudhuri  
> Created_at: 2021-05-07 18:48:34 UTC  
> Updated_at: 2021-05-08 07:31:42 UTC  
> Url: https://github.com/boostorg/gil/pull/604#discussion_r628434096  

I was initially unable to get clang-format 12 but now I have been able to do so.

---

📁 include/boost/gil/image_processing/fast_feature_detector.hpp

```diff
  94 |+ }
  95 |+ }  // namespace detail
  96 |+ template <typename srcview>
```

> Username: mloskot  
> Created_at: 2021-05-02 19:50:23 UTC  
> Updated_at: 2021-05-08 07:31:42 UTC  
> Url: https://github.com/boostorg/gil/pull/604#discussion_r624745929  

blank line

> Username: Sayan-Chaudhuri  
> Created_at: 2021-05-03 15:04:46 UTC  
> Updated_at: 2021-05-08 07:31:42 UTC  
> Url: https://github.com/boostorg/gil/pull/604#discussion_r625151388  

Done

---

📁 include/boost/gil/image_processing/fast_feature_detector.hpp

```diff
 125 |+     // scores to be used during nonmaximum suppression
 126 |+     gray8_view_t FAST_SCORE_MATRIX = view(FAST_image);
 127 |+     fill_pixels(FAST_SCORE_MATRIX, gray8_pixel_t(0));
```

> Username: mloskot  
> Created_at: 2021-05-02 19:51:25 UTC  
> Updated_at: 2021-05-08 07:31:42 UTC  
> Url: https://github.com/boostorg/gil/pull/604#discussion_r624746036  

Please, avoid such UPPERCASE names, unless it's a macro.  
Avoid macros too.

> Username: Sayan-Chaudhuri  
> Created_at: 2021-05-03 15:24:30 UTC  
> Updated_at: 2021-05-08 07:31:42 UTC  
> Url: https://github.com/boostorg/gil/pull/604#discussion_r625165319  

Done


📁 test/core/image_processing/fast_feature_detector.cpp

```diff
  25 |+ }
  26 |+ //testing color image
  27 |+ void test2()
```

> Username: mloskot  
> Created_at: 2021-05-02 19:54:16 UTC  
> Updated_at: 2021-05-08 07:31:42 UTC  
> Url: https://github.com/boostorg/gil/pull/604#discussion_r624746395  

Please, don't avoid blank lines as separator of code blocks/sections. Readability first.

> Username: Sayan-Chaudhuri  
> Created_at: 2021-05-03 15:24:45 UTC  
> Updated_at: 2021-05-08 07:31:42 UTC  
> Url: https://github.com/boostorg/gil/pull/604#discussion_r625165468  

Done


---

## Comment 9

> Username: Sayan-Chaudhuri  
> Created_at: 2021-05-02 20:11:13 UTC  
> Url: https://github.com/boostorg/gil/pull/604#issuecomment-830864927  

@mloskot  Yeah, I am noting down every feedback and trying hard so as not to avoid them. The code is entirely written by me because from our discussion , I understand that copied code is wrong due to several factors. Regarding the blank lines as separator, I actually didnt made any changes after i formatted the code with clang. Ok, so I shall surely add the separators. From your feedback of PR#597, I thought it was bad to mix upper/lower case in filenames. Will keep this mistake in mind. Thanks for taking out time to provide the feedback.

---

## Comment 10

> Username: Sayan-Chaudhuri  
> Created_at: 2021-05-03 15:10:57 UTC  
> Url: https://github.com/boostorg/gil/pull/604#issuecomment-831324171  

@lpranam @mloskot I apologise sincerely for repetitive mistakes. I have looked into the codebase again, read your feedback and have tried to incorporate the necessary changes.

---

## Review 11 [Changes requested]

> Username: mloskot  
> Created_at: 2021-05-06 20:22:04 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/gil/pull/604#pullrequestreview-653861122  

📁 include/boost/gil/image_processing/fast_feature_detector.hpp

```diff
   1 |+ #ifndef BOOST_GIL_IMAGE_PROCESSING_FAST_HPP
   2 |+ #define BOOST_GIL_IMAGE_PROCESSING_FAST_HPP
```

> Username: mloskot  
> Created_at: 2021-05-06 20:21:55 UTC  
> Updated_at: 2021-05-08 07:31:42 UTC  
> Url: https://github.com/boostorg/gil/pull/604#discussion_r627738056  

Please, walk through these points and double, triple check your code against them  
- https://github.com/boostorg/gil/blob/develop/CONTRIBUTING.md#pull-requests  
- https://github.com/boostorg/gil/blob/develop/CONTRIBUTING.md#guidelines

> Username: Sayan-Chaudhuri  
> Created_at: 2021-05-08 07:29:53 UTC  
> Updated_at: 2021-05-08 07:31:42 UTC  
> Url: https://github.com/boostorg/gil/pull/604#discussion_r628715636  

Rechecked and resolved the corresponding issues


---

## Comment 12

> Username: Sayan-Chaudhuri  
> Created_at: 2021-05-10 03:30:13 UTC  
> Url: https://github.com/boostorg/gil/pull/604#issuecomment-836118209  

@mloskot @lpranam  Apologies for the mistakes committed in my previous commit. I have reread the contributing guidelines as instructed by you, and have tried to incorporate each and every instruction from the guidelines. I have doubt over the below mentioned points in the contributing guidelines  
1)All public headers should be placed in boost/gil/ or boost/gil/<component>/.  
  
2)All non-public headers should be placed boost/gil/detail or boost/gil/<component>/detail.  
  
As per my understanding, all the header files that will not be used by a library user should be put in detail folder. But in the already existing header files in the library, I do not find any such convention being followed e.g histogram_equalization.hpp. Also , there is no detail folder inside the image_processing subfolder of boost/gil. So, if you can kindly advice the course of action for this.  
  
I understand repeated mistakes from my side is hindering further course of action from your side regarding the PR. I hqve checked my code several times against the guidelines. I hope that this time, my mistakes will not be hindering the review process. I would kindly request you to have a relook at my code if you find time amidst your busy schedule.

---

## Review 13 [Changes requested]

> Username: mloskot  
> Created_at: 2022-06-25 17:02:27 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/gil/pull/604#pullrequestreview-1019335298  

The `chessboard(2).png` file seems not used anywhere.   
It should be removed.

---
